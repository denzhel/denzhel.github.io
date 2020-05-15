---
layout: post
title: "Troubles of the past: Solaris 10"
date:   2020-05-16
categories: Past
---

## High resolution CPU timing
For low latency/high performance applications, add the following:
```
vi /etc/system

set hires_tick=1
set hires_hz=10000
```

## Disable keys or mouse buttons
Disabling certain keys or mouse buttons can help you achieve a secure desktop/kiosk mode:

Use the following built in utility to get the button ID:
```
/usr/openwin/demo/xev
```

Then, using a script that can be added to the auto start applications, add the line:
```
xmodmap -e "pointer = 1 2 3 4 5 X Y 6 7 ..."
OR
xmodmap -e "keycode 109 = "
```

## The sudo alternative for Solaris 10
Solaris 10 has no sudo command, but you do have pfexec, works the same but different configuration.

Basically, you attach profiles to your user, query them by listing the following file:
```
cat /etc/security/exec_attr

Maintenance and Repair:suser:cmd:::/usr/sbin/poweroff:uid=0
```

Add the profile to your user
```
usermod -P'Maintenance and Repair' <userName>
```

Lastly, run the command using pfexec:
```
pfexec poweroff
```

## Configure NTP Server
Make sure these are the only files in /etc/inet/ntp.conf:
```
server 127.127.1.0
fudge 127.127.1.0 stratum 0
```

Stop and start the service:
```
svcadm disable ntp
svcadm enable ntp
```

## Adding profiles to pfexec
Add your profile:
```
echo "NTP Management:suser:cmd:::/usr/sbin/ntpdate:uid=0" >> /etc/security/exec_attr
```

Add the profile your user
```
usermod -P'NTP Management' <userName>
```

Test the command:
```
pfexec ntpdate -u 1.1.1.1
```

## Rebuild Grub
Insert the installation disc and enter rescue mode
```
cd /a/boot/grub 
installgrub -fm stage1 stage2 /dev/rdsk/c0t0d0s0 
```

Update the boot_archive via bootadm command:
```
bootadm update-archive -fv -R /a
```

Reboot the system

## LD_LIBRARY_CONFIG alternative for Solaris 10
```
crle -u -l /usr/local/lib
```

## Maunally mount a CDROM drive
```
mount -F hsfs /dev/sr0 /cdrom/cdrom0
```

## Sed alternative for Solaris 10
```
perl -pi -e 's/find/replace/g' file
```

## Delete a line in a file with a specific string
```
perl -ni -e 'if(!/test2/){print;}' myfile
```

## WARNING: reboot required
In case you encounter the following message:

```
WARNING: Reboot required.
The system has updated the cache of files (boot archive) that is used
during the early boot sequence. To avoid booting and running the system
with the previously out-of-sync version of these files, reboot the
system from the same device that was previously booted.
```

Run the following command from any shell:
```
svccfg -s svc:/system/boot-config:default setprop config/auto-reboot-safe = true
```

Verify that the command ran successfully:
```
svccfg -s svc:/system/boot-config:default listprop |grep config/auto-reboot-safe
config/auto-reboot-safe            boolean  true
```


## The database might be damaged
Sometimes, the following message could be received:

```
svc.configd: smf(5) database integrity check of:

/etc/svc/repository.db

failed. The database might be damaged or a media error might have
prevented it from being verified. Additional
information useful to
```

Run the following command:
```
/lib/svc/bin/restore_repository
```

Then, enter one of the boot backup you see on the screen, for e.g:
```
boot-20151210_06069
```
