---
layout: post
title: "Troubles of the past: Cisco"
date:   2020-04-28
categories: Past
---

## Configure login with username

```
username admin privilege 15 password cisco
conf t
no aaa new-model
line con 0
login local
```

## Configure logging
By default, cisco switches are not configured with saving history, you can use
the following to enable it:

```
conf t
archive
log config
logging enable 100
notify syslog
hidekeys
```

To watch the history of commands:

```
show archive log config all
```

## Configure port mirroring

```
monitor session 1 source interface gigabitethernet0/1
monitor session 1 destination interface gigabitethernet0/2
```

## Remove port security configuration

```
conf t
int g1/0/<portNumber>
shutdown
no switchport port-security
no shutdown
```

## Cisco switch cannot save configuration

If you're having troubles with saving your configuration using:

```
wr
```

Please check the register type and then try again:

```
conf terminal
no system ignore startupconfig switch all
exit
config-register 0x102
write mem
reload
```

## Convert bundle to install mode

```
software expand running to flash:
configure terminal 
boot system switch all flash:packages.conf 
exit
wr
reload
````

## Cleanup old versions

Cisco switches have limited disk space, therefor, you can use the following to clean old image versions:

```
software clean
```

## Install new IOS version

To install a new version, use the following:

```
software install file flash:<New_IOS_File_Name>.bin
```

Make sure that manual boot is not enabled:

```
show boot
no boot manual
reload
```

## Upgrade license level

If you want various features such as Multicase, you need to upgrade the license level:

```
license right-to-use activate ip-services all accepteula
```
