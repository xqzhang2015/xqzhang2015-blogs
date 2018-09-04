---
title: "The journal of debug lightty access log not appending issue"
description: ""
tags: [
    "C++",
]
date: "2018-09-04 11:42:31"
lastmod: "2018-09-04 11:42:57"
categories: [
	"architecture",
    "web server",
    "linux",
    "arch",
    "C++",
]
---

## What I learned

### fcntl(fd, F_SETFL, O_NONBLOCK);

It will clear all file status flags firstly.

### UML: user mode linux

With it, we could GDB linux system call.

[CSDN: UML(User Mode Linux) -- built binaries](https://blog.csdn.net/kaikai_sk/article/details/79614842)<br/>

[阿里云: User mode Linux -- self generate](https://www.aliyun.com/jiaocheng/174196.html)<br/>

## SYSTEM call

If fcntl() is called, file flags will be cleared and set with the new arg

{{% more code %}}
```c++
SYSCALL_DEFINE3(fcntl, unsigned int, fd, unsigned int, cmd, unsigned long, arg)
{
    struct file *filp;
    filp = fget_raw(fd);

    err = do_fcntl(fd, cmd, arg, filp);
}

static long do_fcntl(int fd, unsigned int cmd, unsigned long arg,
        struct file *filp)
{
    switch (cmd) {
    // ...
    case F_SETFD:
        err = 0;
        set_close_on_exec(fd, arg & FD_CLOEXEC);
        break;
    case F_SETFL:
        err = setfl(fd, filp, arg);
        break;
    // ...
    }

}

#define SETFL_MASK (O_APPEND | O_NONBLOCK | O_NDELAY | O_DIRECT | O_NOATIME)

static int setfl(int fd, struct file * filp, unsigned long arg)
{
    struct inode * inode = filp->f_path.dentry->d_inode;
    int error = 0;

    /*
     * O_APPEND cannot be cleared if the file is marked as append-only
     * and the file is open for write.
     */
    // [GDB] b setfl if strncmp(filp->f_path->dentry->d_iname, "access", 6) == 0
    if (((arg ^ filp->f_flags) & O_APPEND) && IS_APPEND(inode)) // to confirm ...
        return -EPERM;

    /* O_NOATIME can only be set by the owner or superuser */
    if ((arg & O_NOATIME) && !(filp->f_flags & O_NOATIME))
        if (!inode_owner_or_capable(inode))
            return -EPERM;

    /* required for strict SunOS emulation */
    if (O_NONBLOCK != O_NDELAY)
           if (arg & O_NDELAY)
           arg |= O_NONBLOCK;

    if (arg & O_DIRECT) {
        if (!filp->f_mapping || !filp->f_mapping->a_ops ||
            !filp->f_mapping->a_ops->direct_IO)
                return -EINVAL;
    }

    if (filp->f_op && filp->f_op->check_flags)
        error = filp->f_op->check_flags(arg);
    if (error)
        return error;

    /*
     * ->fasync() is responsible for setting the FASYNC bit.
     */
    if (((arg ^ filp->f_flags) & FASYNC) && filp->f_op &&
            filp->f_op->fasync) {
        error = filp->f_op->fasync(fd, filp, (arg & FASYNC) != 0);
        if (error < 0)
            goto out;
        if (error > 0)
            error = 0;
    }
    spin_lock(&filp->f_lock);
    filp->f_flags = (arg & SETFL_MASK) | (filp->f_flags & ~SETFL_MASK); // the key point ...
    spin_unlock(&filp->f_lock);

 out:
    return error;
}
```
{{% /more %}}


## Append only mode

### chattr
```shell
NAME
       chattr - change file attributes on a Linux file system

SYNOPSIS
       chattr [ -RVf ] [ -v version ] [ mode ] files...

DESCRIPTION
       The format of a symbolic mode is +-=[aAcCdDeijsStTu].

       The operator '+' causes the selected attributes to be added to the existing attributes of the files; '-' causes them to be removed; and

       append only (a), no atime updates (A), compressed (c), no copy on write (C), ...
```

### lsattr and example


`chattr` to set file attribute, `lsattr` to show.

```shell
x~/docker/test$ ll access.log
-rw-r--r-- 1 userA eng 168 Sep  3 16:11 access.log

x~/docker/test$ chattr +a access.log
chattr: Operation not permitted while setting flags on access.log

x~/docker/test$ sudo chattr +a access.log

x~/docker/test$ ll access.log
-rw-r--r-- 1 userA eng 168 Sep  3 16:11 access.log

~/docker/test$ lsattr access.log
-----a---------- access.log
```
