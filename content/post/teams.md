---
title: "MS Teams on openSUSE"
date: 2020-06-22T10:33:10+02:00
draft: false
---

One way to install Microsoft Teams on openSUSE is to download the [RPM file](https://www.microsoft.com/de-de/microsoft-365/microsoft-teams/download-app#desktopAppDownloadregion) and install it on the command line:

```bash
$ ll | grep teams
-rw-r--r-- 1 dom users  89M 2020-06-22 10:31 teams-1.3.00.5153-1.x86_64.rpm

$ rpm -i teams-1.3.00.5153-1.x86_64.rpm
```

Another way is to add the Microsoft repository and install it via zypper:

```bash
rpm --import https://packages.microsoft.com/keys/microsoft.asc
zypper ar https://packages.microsoft.com/yumrepos/ms-teams teams
zypper in teams
```
