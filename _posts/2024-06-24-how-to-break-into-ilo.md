---
layout: blog
title: "How To: Break Into iLO"
date: 2024-06-24
permalink: /blog/how-to-break-into-ilo/
---

<br/>

[←](/blog)

Integrated Lights-Out, or iLO, is a proprietary embedded server management technology by Hewlett-Packard Enterprise which provides out-of-band management facilities.

<br/>

## What does that mean?
iLO allows you to manage an HPE server at the host level over a network, regardless of power state.

<br/>

## When is this necessary?
If you cannot log into iLO for an HPE host because of a lost/forgotten password or a previous systems administrator did not provide credentials.

<br/>

## Prerequisites
This specifically pertains to situations where the iLO is installed on an ESXi (VMware) and you have access to the ESXi.

If you do not have access to the ESXi, but do have access to the vCenter, you can reset the password by exporting, rewriting, and replacing the host profile, then performing a reboot.

*note*: This does require a VMware Enterprise Plus license

[VMware KB](https://knowledge.broadcom.com/external/article?legacyId=68079)<br/><br/>
[Third-party KB](https://www.vmwarearena.com/reset-esxi-root-password-using-vmware-host-profiles/)

<br/>

## How To: Crack into iLO
1. Log into the vCenter/ESXi where the iLO connected to the host resides

2. Turn on SSH
This can be done under:
vcenter.example.com > Host > Configure > System > Services > SSH > Start

3. SSH into host using PuTTy or terminal of choice with ESXi credentials

4. Switch to the /opt/tools directory where hponcfg resides and find the hponcfg file

```
cd /opt/tools
ls -l
```

5. Write the hponcfg file to a currentcfg file for backup purposes

```
./hponcfg -w currentcfg
```

Feel free to take a look at the output using something like vi

6. Create an XML file we will use to reset the Administrator password

```
touch pwreset.xml
```

7. Hop into a text editor and paste the following text below:

```
vi pwreset.xml
```

Copy/Paste:

```
<RIBCL VERSION="2.1">
 <LOGIN USER_LOGIN="Administrator" PASSWORD="password">
  <USER_INFO MODE="write">
  <MOD_USER USER_LOGIN=”Administrator”>
  <password value=”newpassword”/>
  </MOD_USER>
  </USER_INFO>
 </LOGIN>
</RIBCL>
```

8. Replace "newpassword" with a password of your choice

9. Write and quit out of your text editor to save the XML file changes

```
<Esc key>
:wq
```

10. Write the XML file contents to the hponcfg file to perform the password reset

```
./hponcfg -w pwreset.xml
```

11. You should now be able to log into the iLO interface!

## Cleanup
Don't forget to clean up your room after playing with your toys


1. Remove the currentcfg and pwreset.xml files after you confirm everything is correctly working after the password reset

```
rm -f currentcfg pwreset.xml
```

2. Turn off the SSH service for the host

vcenter.example.com > Host > Configure > System > Services > SSH > Stop