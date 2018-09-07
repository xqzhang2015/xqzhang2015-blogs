---
title: "The journal of linux logrotate"
description: ""
tags: [
    "arch",
    "linux"
]
date: "2018-09-06 11:42:31"
lastmod: "2018-09-07 11:42:57"
categories: [
	"arch",
    "web server",
    "linux",
]
---

## Overview

test

### Linux system cronjob

* cronjob: hourly, daily, ...

```shell
[root@xxx run]# cd /etc/cron
cron.d/       cron.daily/   cron.deny     cron.hourly/  cron.monthly/ crontab       cron.weekly/
```

* logrotate

```
[root@xxx cron.daily]# pwd
/etc/cron.daily
[root@xxx cron.daily]# cat logrotate
#!/bin/sh

/usr/sbin/logrotate -s /var/lib/logrotate/logrotate.status /etc/logrotate.conf
EXITVALUE=$?
if [ $EXITVALUE != 0 ]; then
    /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0
```

## Knowledge

### crontab knowledge


##### Job format
By default, nnCron uses the enhanced format.

The following graph shows what it consists of:

```shell
* * * * * *
| | | | | | 
| | | | | +-- Year              (range: 1900-3000)
| | | | +---- Day of the Week   (range: 1-7, 1 standing for Monday)
| | | +------ Month of the Year (range: 1-12)
| | +-------- Day of the Month  (range: 1-31)
| +---------- Hour              (range: 0-23)
+------------ Minute            (range: 0-59)
```

##### Crontab commands

export EDITOR=vi ;to specify a editor to open crontab file.

1. crontab -e    Edit crontab file, or create one if it doesn’t already exist.
2. crontab -l    crontab list of cronjobs , display crontab file contents.
3. crontab -r    Remove your crontab file.
4. crontab -v    Display the last time you edited your crontab file. (This option is only available on a few systems.)



##### Examples

{{% notice tip %}}
(1) \* in the value field above means all legal values as in braces for that column.<br/>
(2) Repeat pattern like /2 for every 2 minutes or /10 for every 10 minutes 
{{% /notice %}}

```shell
* * * * * *                         Each minute

59 23 31 12 5 *                     One minute  before the end of year if the last day of the year is Friday
									
59 23 31 DEC Fri *                  Same as above (different notation)

45 17 7 6 * *                       Every  year, on June 7th at 17:45

45 17 7 6 * 2001,2002               Once a   year, on June 7th at 17:45, if the year is 2001 or  2002

0,15,30,45 0,6,12,18 1,15,31 * 1-5 *  At 00:00, 00:15, 00:30, 00:45, 06:00, 06:15, 06:30,
                                    06:45, 12:00, 12:15, 12:30, 12:45, 18:00, 18:15,
                                    18:30, 18:45, on 1st, 15th or  31st of each  month, but not on weekends

*/15 */6 1,15,31 * 1-5 *            Same as above (different notation)

0 12 * * 1-5 * (0 12 * * Mon-Fri *) At midday on weekdays

* * * 1,3,5,7,9,11 * *              Each minute in January,  March,  May, July, September, and November

1,2,3,5,20-25,30-35,59 23 31 12 * * On the  last day of year, at 23:01, 23:02, 23:03, 23:05,
                                    23:20, 23:21, 23:22, 23:23, 23:24, 23:25, 23:30,
                                    23:31, 23:32, 23:33, 23:34, 23:35, 23:59

0 9 1-7 * 1 *                       First Monday of each month, at 9 a.m.

0 0 1 * * *                         At midnight, on the first day of each month

* 0-11 * * *                        Each minute before midday

* * * 1,2,3 * *                     Each minute in January, February or March

* * * Jan,Feb,Mar * *               Same as above (different notation)

0 0 * * * *                         Daily at midnight

0 0 * * 3 *                         Each Wednesday at midnight
```



