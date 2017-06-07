---
title: Taskwarrior for Windows
excerpt: Running taskwarrior from cmder without docker container
categories: code other
layout: post
---

### Premise

[Taskwarrior](http://taskwarrior.org) is a very nice light-weight todo manager available for *nix systems. For windows they primarily support bash for windows and a version that comes packaged with [cygwin](https://cygwin.com/).

Personally, I have started setting up a command line environment using [cmder](http://cmder.net/), which brings some *nix tools to windows but is properly integrated with the Windows system. Obviously it would be preferable to use taskwarrior from this shell instead of having a separate just for the todo lists.

### Using Cygwin distributed executable from cmder

I stumbled across [this post](http://www.cnblogs.com/xdao/p/taskwarrior_win.html) that describes how to extract taskwarrior from cygwin:

1. Download [cygwin](https://cygwin.com/)
1. During install, make sure to select taskwarrior package
1. Search for the `task.exe` from `cygwin\bin`, copy it over to the `cmder\bin` folder
1. Typing `task` in a new cmder window will alert you that several dlls are missing, copy those over as well:

```
set CMDBINDIR=D:\Path\To\cmder\bin
set CYGBINDIR=D:\Path\To\cygwin\bin
cp %CYGBINDIR%\cyggcc_s-seh-1.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cyghogweed-2.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cygnettle-4.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cygtasn1-6.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cygz.dll      %CMDBINDIR%\.
cp %CYGBINDIR%\cyggmp-10.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cygiconv-2.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cygp11-kit-0.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cyguuid-1.dll %CMDBINDIR%\.
task.exe*
cp %CYGBINDIR%\cygffi-6.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cyggnutls-28.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cygintl-8.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cygstdc++-6.dll %CMDBINDIR%\.
cp %CYGBINDIR%\cygwin1.dll %CMDBINDIR%\.
```

And that's it. I also linked my Dropbox folder that contains the taskwarrior data into my `%HOMEPATH%\.task` so that I can share the data accross platforms and PCs.
