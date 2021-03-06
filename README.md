# Invoke-Recon
Powershell script as a first step for domain enumeration. Tries to spot quickwins.  
Just because i'm tired to type the same AD / PowerView commands over and over.  
  
# Prerequisites  
You may want to exclude your tools directory from Defender (if you clone submodules for examples):  
```
Add-MpPreference -ExclusionPath "C:\Users\bleponge\Documents\myrepos"
Get-MpPreference | Select -Expand ExclusionPath
```  
  
If you don't already have imported the following modules for you enumeration:    
```
git clone --recurse-submodules https://github.com/phackt/Invoke-Recon.git && cd .\Invoke-Recon
Import-Module .\modules\PowerSploit\Recon\PowerView.ps1
Import-Module .\modules\PowerUpSQL\PowerUpSQL.psd1
Import-Module .\modules\ADModule\Microsoft.ActiveDirectory.Management.dll
Import-Module .\modules\ADModule\ActiveDirectory\ActiveDirectory.psd1
```  
  
# Run
```
.\Invoke-Recon.ps1 -Domain us.funcorp.local | Tee-Object -FilePath .\invoke-recon.txt

################################################################
################################################################
| Starting enumeration of domain us.funcorp.local
################################################################
################################################################

+------+------------------------------------------------+------+
| Searching PDC
+------+------------------------------------------------+------+

Name                                     Type   TTL   Section    NameTarget                     Priority Weight Port
----                                     ----   ---   -------    ----------                     -------- ------ ----
_ldap._tcp.pdc._msdcs.us.funcorp.local   SRV    600   Answer     UFC-DC1.us.funcorp.local       0        100    389

Name       : UFC-DC1.us.funcorp.local
QueryType  : A
TTL        : 600
Section    : Additional
IP4Address : 192.168.2.1


+------+------------------------------------------------+------+
| Searching all DCs
+------+------------------------------------------------+------+
_ldap._tcp.dc._msdcs.us.funcorp.local    SRV    600   Answer     UFC-DC1.us.funcorp.local       0        100    389

Name       : UFC-DC1.us.funcorp.local
QueryType  : A
TTL        : 600
Section    : Additional
IP4Address : 192.168.2.1


+------+------------------------------------------------+------+
| Checking spooler service is up on DCs
+------+------------------------------------------------+------+
...


+------+------------------------------------------------+------+
| Members of the DCs 'Domain Local' group Administrators
+------+------------------------------------------------+------+
[+] Digging into 192.168.2.1

ComputerName : 192.168.2.1
GroupName    : Administrators
MemberName   : USFUN\Administrator
SID          : S-1-5-21-3965405831-1015596948-2589850225-500
IsGroup      : False
IsDomain     : False
...


+------+------------------------------------------------+------+
| Nested privileged users (RID >= 1000)
+------+------------------------------------------------+------+

GroupDomain             : us.funcorp.local
GroupName               : Domain Admins
GroupDistinguishedName  : CN=Domain Admins,CN=Users,DC=us,DC=funcorp,DC=local
MemberDomain            : us.funcorp.local
MemberName              : servicesadmin
MemberDistinguishedName : CN=services admin,CN=Users,DC=us,DC=funcorp,DC=local
MemberObjectClass       : user
MemberSID               : S-1-5-21-3965405831-1015596948-2589850225-1122


+------+------------------------------------------------+------+
| Looking for Exchange servers
+------+------------------------------------------------+------+

Version       : 15.1.1531
FQDN          : MAIL01.funcorp.local
Roles         : UM CAS MB HT
Class         : top server msExchExchangeServer
PrivExchange  : True
CVE-2020-0688 : True

[!] Exchange server MAIL01.funcorp.local vulnerable to PrivExchange
[!] Exchange server MAIL01.funcorp.local vulnerable to CVE-2020-0688


------+------------------------------------------------+------+
| Computers with constrained delegation and protocol transition
+------+------------------------------------------------+------+
...


+------+------------------------------------------------+------+
| Users with constrained delegation and protocol transition
+------+------------------------------------------------+------+
...


+------+------------------------------------------------+------+
| Managed Service Accounts with constrained delegation and protocol transition
+------+------------------------------------------------+------+
...


+------+------------------------------------------------+------+
| Finding principals with replicating permissions
+------+------------------------------------------------+------+
...

[more]
```

# Todo
- https://github.com/NetSPI/PowerUpSQL/pull/62 - -DomainController using current security context (no creds supplied)
- Finding all others common quick wins (privexchange, cve-2020-0688, ...)
- Cross the results
