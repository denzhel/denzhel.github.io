---
layout: post
title: "Troubles of the past: Solaris 10"
date:   2020-05-16
categories: Past
---

## High resolution CPU timing
For low latency/high performance applications, add the following:
```shell
vi /etc/system

set hires_tick=1
set hires_hz=10000
```

## Disable keys or mouse buttons
Disabling certain keys or mouse buttons can help you achieve a secure desktop/kiosk mode:

Use the following built in utility to get the button ID:
```shell
/usr/openwin/demo/xev
```

Then, using a script that can be added to the auto start applications, add the line:
```shell
xmodmap -e "pointer = 1 2 3 4 5 X Y 6 7 ..."
OR
xmodmap -e "keycode 109 = "
```

## The sudo alternative for Solaris 10
Solaris 10 has no sudo command, but you do have pfexec, works the same but different configuration.

Basically, you attach profiles to your user, query them by listing the following file:
```shell
cat /etc/security/exec_attr

Maintenance and Repair:suser:cmd:::/usr/sbin/poweroff:uid=0
```

Add the profile to your user:
```shell
usermod -P'Maintenance and Repair' <userName>
```

Lastly, run the command using pfexec:
```shell
pfexec poweroff
```

## Configure NTP Server
Make sure these are the only files in /etc/inet/ntp.conf:
```shell
server 127.127.1.0
fudge 127.127.1.0 stratum 0
```

Stop and start the service:
```shell
svcadm disable ntp
svcadm enable ntp
```

## Adding profiles to pfexec
Add your profile:
```shell
echo "NTP Management:suser:cmd:::/usr/sbin/ntpdate:uid=0" >> /etc/security/exec_attr
```

Add the profile to your user:
```shell
usermod -P'NTP Management' <userName>
```

Test the command:
```shell
pfexec ntpdate -u 1.1.1.1
```

## Rebuild Grub
Insert the installation disc and enter rescue mode
```shell
cd /a/boot/grub 
installgrub -fm stage1 stage2 /dev/rdsk/c0t0d0s0 
```

Update the boot_archive via bootadm command:
```shell
bootadm update-archive -fv -R /a
```

Reboot the system

## LD_LIBRARY_CONFIG alternative for Solaris 10
```shell
crle -u -l /usr/local/lib
```

## Maunally mount a CDROM drive
```shell
mount -F hsfs /dev/sr0 /cdrom/cdrom0
```

## Sed alternative for Solaris 10
```shell
perl -pi -e 's/find/replace/g' file
```

## Delete a line in a file with a specific string
```shell
perl -ni -e 'if(!/test2/){print;}' myfile
```

## WARNING: reboot required
In case you encounter the following message:

```shell
WARNING: Reboot required.
The system has updated the cache of files (boot archive) that is used
during the early boot sequence. To avoid booting and running the system
with the previously out-of-sync version of these files, reboot the
system from the same device that was previously booted.
```

Run the following command from any shell:
```shell
svccfg -s svc:/system/boot-config:default setprop config/auto-reboot-safe = true
```

Verify that the command ran successfully:
```shell
svccfg -s svc:/system/boot-config:default listprop |grep config/auto-reboot-safe
config/auto-reboot-safe            boolean  true
```


## The database might be damaged
Sometimes, the following message could be received:

```shell
svc.configd: smf(5) database integrity check of:

/etc/svc/repository.db

failed. The database might be damaged or a media error might have
prevented it from being verified. Additional
information useful to
```

Run the following command:
```shell
/lib/svc/bin/restore_repository
```

Then, enter one of the boot backup you see on the screen, for e.g:
```shell
boot-20151210_06069
```

## Configure a NIS client
Add the default domain to a file:
```shell
echo something >> /etc/defaultdomain
```

Add the following to nsswitch.conf(the word nis to the following fields):
```shell
passwd: files nis
shadow: files nis
group: files nis
```

Add your NIS servers to /etc/hosts(this fixes a bug in Solaris 10):
```shell
server1 192.168.0.1
server2 192.168.0.2
```

Run the following command to finish the setup:
```shell
ypinit -c
```

Make sure the service will come up on boot:
```shell
svcadm enable nis/client
```

## Cron repetitive task
The usual syntax known in Linux is not fully compatible with Solaris 10, therefor, use the following:
```
0,5,10,15,20,25,30,35,40,45,50,55 * * * * /usr/local/bin/fix_ntp
```

## Disable GUI login
Run the following command:
```shell
/usr/dt/bin/dtconfig -d
```

Next, disable the service on startup:
```
svcadm disable svc:/application/graphical-login/cde-login:default
```

## Network Routes
Add a network route:
```shell
route -p add <Network> -netmask <Netmask> <Gateway>
e.g.
route -p add 10.10.10.0 -netmask 255.255.255.0 192.168.1.1
```

Add a default route:
```shell
route -p add default 192.168.1.1
```

Add a multicast route:
```shell
route add 224.0/4 `uname -n`
```

Add a route to a specific interface(recommended method):
```shell
route add -host 1.1.1.1 1.1.1.254 -ifp igb0
```

To delete all routes:
```shell
route flush
```

## Configure Autologin
Solaris 10 does not allow you to configure an auto login out of the box.

Edit /etc/X11/gdm/gdm.conf and change the following:
```shell
AutomaticLoginEnable=true
AutomaticLogin=dennis
GdmXserverTimeout=30
```

Disable the old cde login and enable the new one:
```shell
svcadm disable cde-login
svcadm enable gdm2-login
```

Add the following lines to /etc/pam.conf
```shell
gdm-autologin auth  required    pam_unix_cred.so.1
gdm-autologin auth  sufficient  pam_allow.so.1
```

Create a file with the following content:
```shell
vi /tmp/pam_allow.c
```

```c
#include <stdio.h>
#include <security/pam_appl.h>

/*
 * This code may be used to create a pam_allow.so.1 PAM module
 * to allow Automatic Login to work on Solaris 10 or lower.
 *
 * Compile:
 *	cc pam_allow.c -o pam_allow.so.1 -Kpic -G
 */
int 
pam_sm_chauthtok(pam_handle_t *pamh, int flags, int argc, const char **argv)
{
	return (PAM_SUCCESS);
}

int
pam_sm_authenticate(pam_handle_t *pamh, int flags, int argc, const char **argv)
{
	return (PAM_SUCCESS);
}


int
pam_sm_setcred(pam_handle_t *pamh, int flags, int argc, const char **argv)
{
	return (PAM_SUCCESS);
}

int
pam_sm_acct_mgmt(pam_handle_t *pamh, int flags, int argc, const char **argv)
{
	return (PAM_SUCCESS);
}

int
pam_sm_open_session(pam_handle_t *pamh, int flags, int argc, const char **argv)
{
	return (PAM_SUCCESS);
}

int
pam_sm_close_session(pam_handle_t *pamh, int flags, int argc, const char **argv)
{
	return (PAM_SUCCESS);
}
```

Make sure Sun Studio is installed and run the following:
```
cc pam_allow.c -o /usr/lib/security/pam_allow.so.1 -Kpic -G
```

If problems occur, please create .dmrc file inside the user's home directory with the following content:

[Desktop]

Session=gnome

Also, please make sure that the user with the auto login have the proper permissions:
```shell
chmod 755 /home/user
```
```
