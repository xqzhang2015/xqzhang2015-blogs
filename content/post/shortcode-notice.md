---
title: "Shortcodes Notice Preview"
date: 2018-08-20 01:17:14
lastmod: 2018-08-20 01:17:29
draft: false
tags: ["preview", "shortcodes", "tag-6"]
categories: ["docs", "shortcodes", "index"]
---

## normal use
`Note` example:

```shortcode
{{%/* notice note */%}}
A notice disclaimer
{{%/* /notice */%}}
```

**Result:**
{{% notice note %}}
A notice disclaimer
{{% /notice %}}


You could **custom title** :


```shortcode
{{%/* notice note 笔记 */%}}
A notice disclaimer
{{%/* /notice */%}}
```

{{% notice note 笔记 %}}
A notice disclaimer
{{% /notice %}}


## tip

```shortcode
{{%/* notice tip */%}}
A tip disclaimer
{{%/* /tip */%}}
```

{{% notice tip %}}
A tip disclaimer
{{% /notice %}}

### tip-1th

```shortcode
I am a test code, 1th.
```

### tip-2th

```shortcode
I am a test code, 2th.
```

{{% notice warning %}}
I am a warning test, 2th
{{% /notice %}}

## info
```shortcode
{{%/* notice info */%}}
A info disclaimer
{{%/* /notice */%}}
```

{{% notice info %}}
An information disclaimer
{{% /notice %}}


## warning
```shortcode
{{%/* notice warning */%}}
A warning disclaimer
{{%/* /notice */%}}
```

{{% notice warning %}}
An warning disclaimer
{{% /notice %}}
