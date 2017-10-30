---
layout: post
title: Configure a samba server
date: 2014-08-06 10:44:45.000000000 +02:00
type: post
published: true
categories:
- linux
---

```bash
# disable iptables
myhost $ sudo /etc/init.d/iptables stop
myhost $ sudo /sbin/chkconfig iptables off
myhost $ sudo /sbin/chkconfig --list iptables
iptables 0:off 1:off 2:off 3:off 4:off 5:off 6:off
# allow write access having SELinux on
myhost $ sudo /usr/sbin/setsebool -P samba_export_all_rw on
# enable and start samba server
myhost $ sudo /etc/init.d/smb restart
myhost $ sudo /sbin/chkconfig smb on
myhost $ sudo /sbin/chkconfig --list smb
smb 0:off 1:off 2:on 3:on 4:on 5:on 6:off
# check if i can log in as %username%
myhost $ smbclient -U username -L //myhost
Password:
session setup failed: NT_STATUS_LOGON_FAILURE
# add %username% to the samba users
myhost $ sudo smbpasswd -a username
New SMB password:
Retype new SMB password:
Added user username.
```
