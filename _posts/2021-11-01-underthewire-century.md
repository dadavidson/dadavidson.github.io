---
layout: post
title: "Under The Wire | Century"
date: 2021-11-01 22:18:06
tags: [UnderTheWire, Wargame, Powershell]
style: border
color: primary
description: Under The Wire - Century Walkthrough
---

![](https://i.imgur.com/O4Gzby8.jpg)

Many of you may have read my series on a Linux command line wargame site called [OverTheWire](https://overthewire.org/information/staff.html). If you'd like to check it out be sure to read all about it [here](https://dadavidson.github.io/tags/overthewire/). 

That said, what if there was a similar wargame platform for Windows, Powershell, and other cool sysadmin-like stuff? Well the UTW team has done just that with [UnderTheWire](https://underthewire.tech). UnderTheWire is a cool website that hosts several PowerShell-based wargames meant as "PowerShell Training for the People."

In this new series, I'll be featuring my walkthrough of the first set of challenges or *"wargames"* called [Century](https://underthewire.tech/century). If you would like to jump to a specific level then each level with be accompanied with the username and password. I will be appending consecutive levels as I have time to post. 

That said, I want to give a huge thank you to the creators of these games and for the effort that goes into designing and hosting them, and of course making them available for free!

---

## Century 0 -> 1
[https://underthewire.tech/century](https://underthewire.tech/century)

The goal of this level is to log into the game.

At the time of this writing, the Slack link was dead so I had to lean on this [Reddit thread](https://www.reddit.com/r/PowerShell/comments/n6yxrp/does_anybody_knows_if_the_underthewiretech/).


**Username:** century1

**Password:** century1

---

## Century 1 -> 2
[https://underthewire.tech/century-1](https://underthewire.tech/century-1)

The password for Century2 is the build version of the instance of PowerShell installed on this system.

NOTE:

- The format is as follows: \*\*.\*.\*\*\*\*\*.\*\*\*\*
- Include all periods
- Be sure to look for build version and NOT PowerShell version

```console
PS C:\users\century1\desktop> $PSVersionTable

Name                           Value
----                           -----
PSVersion                      5.1.14393.4467
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.14393.4467
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

**Username:** century2

**Password:** 10.0.14393.4467

---

## Century 2 -> 3
[https://underthewire.tech/century-2](https://underthewire.tech/century-2)

> "The password for Century3 is the name of the built-in cmdlet that performs the wget like function within PowerShell PLUS the name of the file on the desktop."

To find the command similar to `wget` within PowerShell use the `Get-Alias` cmdlet.

```console
PS C:\users\century2\desktop> get-alias wget

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           wget -> Invoke-WebRequest
```

We can also get the name of the file on the desktop.

```console
PS C:\users\century2\desktop> ls


    Directory: C:\users\century2\desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/30/2018   3:29 AM            693 443
```

**Username:** century3

**Password:** invoke-webrequest443

---

## Century 3 -> 4
[https://underthewire.tech/century-3](https://underthewire.tech/century-3)

> "The password for Century4 is the number of files on the desktop."

1. You can use 'Get-Childtem' to list the directory contents or 'ls' which is an alias. 
2. By enclosing a command with `()` you create an order of precedence, so everything inside of `()` executes first and after that, your other commands are evaluated.
3. We use `Measure-Object` to get the number of files on the desktop
4. `.` Property dereference operator to drill down to the specific `.Count` method of the object

```console
PS C:\users\century3\desktop> (Get-ChildItem | Measure-Object).Count
123
```

**Username:** century4

**Password:** 123

---

## Century 4 -> 5
[https://underthewire.tech/century-4](https://underthewire.tech/century-4)

> "The password for Century5 is the name of the file within a directory on the desktop that has spaces in its name."

Two ways you could do this level.

**Method 1:**

You could use `tree` with the `/F` option to display the names of the files in each folder. This also grabs hidden files. For the challenge, this reveals only 1 of the directories containing a file thus that file is our password.

```console
PS C:\users\century4\desktop> tree /F
Folder PATH listing for volume Windows
Volume serial number is 000000A7 641F:3922
C:.
│   file.txt
│   files.txt
│
├───Can You Open Me
│       61580
│
├───One      Directory
├───Open              Me
└───Open     Me
PS C:\users\century4\desktop>
```

**Method 2:**

Using `Get-Childitem -Recurse` recursively returns each file and folder from a specified path. Again, for the challenge, this reveals only 1 of the directories containing a file thus that file is our password.

```console
PS C:\users\century4\desktop> Get-ChildItem -Recurse


    Directory: C:\users\century4\desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         7/3/2021   1:15 AM                Can You Open Me
d-----         3/2/2021   6:57 PM                One      Directory
d-----         7/3/2021   1:17 AM                Open              Me
d-----        3/14/2021   9:11 PM                Open     Me
-a----         7/9/2021   6:07 PM            272 file.txt
-a----        8/24/2021  12:17 AM           1486 files.txt


    Directory: C:\users\century4\desktop\Can You Open Me


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/30/2018   3:29 AM             24 61580


PS C:\users\century4\desktop>
```

**Username:** century5

**Password:** 61580

---

## Century 5 -> 6
[https://underthewire.tech/century-5](https://underthewire.tech/century-5)

> "The password for Century6 is the short name of the domain in which this system resides in PLUS the name of the file on the desktop."

First, we need to find the domain.

**Method 1:** 

Using the `$env:USERDOMAIN` environment variable.

```console
PS C:\users\century5\desktop> echo $env:USERDNSDOMAIN
UNDERTHEWIRE.TECH
```

**Method 2:**

Using `Get-ADDomain` cmdlet.

```console
PS C:\users\century5\desktop> Get-ADDomain             y  


AllowedDNSSuffixes                 : {}
ChildDomains                       : {}
ComputersContainer                 : CN=Computers,DC=underthewire,DC=tech
DeletedObjectsContainer            : CN=Deleted Objects,DC=underthewire,DC=tech
DistinguishedName                  : DC=underthewire,DC=tech
DNSRoot                            : underthewire.tech
DomainControllersContainer         : OU=Domain Controllers,DC=underthewire,DC=tech
DomainMode                         : Windows2016Domain
DomainSID                          : S-1-5-21-758131494-606461608-3556270690
ForeignSecurityPrincipalsContainer : CN=ForeignSecurityPrincipals,DC=underthewire,DC=tech
Forest                             : underthewire.tech
InfrastructureMaster               : utw.underthewire.tech
LastLogonReplicationInterval       :
LinkedGroupPolicyObjects           : {cn={ECB4A7C0-B4E1-41B1-9E89-161CFA679999},cn=policies,cn=system,DC=underthewire,DC=tech,
                                      CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=underthewire,DC=tech}
LostAndFoundContainer              : CN=LostAndFound,DC=underthewire,DC=tech
ManagedBy                          :
Name                               : underthewire
NetBIOSName                        : underthewire
ObjectClass                        : domainDNS
ObjectGUID                         : bdccf3ad-b495-4d86-a94c-60f0d832e6f0
ParentDomain                       :
PDCEmulator                        : utw.underthewire.tech
PublicKeyRequiredPasswordRolling   : True
QuotasContainer                    : CN=NTDS Quotas,DC=underthewire,DC=tech
ReadOnlyReplicaDirectoryServers    : {}
ReplicaDirectoryServers            : {utw.underthewire.tech}
RIDMaster                          : utw.underthewire.tech
SubordinateReferences              : {DC=ForestDnsZones,DC=underthewire,DC=tech, DC=DomainDnsZones,DC=underthewire,DC=tech,    
                                     CN=Configuration,DC=underthewire,DC=tech}
SystemsContainer                   : CN=System,DC=underthewire,DC=tech
UsersContainer                     : CN=Users,DC=underthewire,DC=tech
```

Or more specifically...

```console
PS C:\users\century5\desktop> (Get-ADDomain).Name
underthewire
```

Lastly, we need the name of the file on the desktop.

```console
PS C:\users\century5\desktop> ls


    Directory: C:\users\century5\desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/30/2018   3:29 AM             54 3347
```

**Username:** century6

**Password:** underthewire3347

---

## Century 6 -> 7
[https://underthewire.tech/century-6](https://underthewire.tech/century-6)

> "The password for Century7 is the number of folders on the desktop."

Similar to level 3 we can use the `Measure-Object` cmdlet to count the number of folders for us.

```console
PS C:\users\century6\desktop> Get-ChildItem | Measure-Object


Count    : 197
Average  :
Sum      :
Maximum  :
Minimum  :
Property :
```

More specifically...

```console
PS C:\users\century6\desktop> (Get-ChildItem | Measure-Object).Count
197
```

**Username:** century7

**Password:** 197

---

## Century 7 -> 8
[https://underthewire.tech/century-7](https://underthewire.tech/century-7)

> "The password for Century8 is in a readme file somewhere within the contacts, desktop, documents, downloads, favorites, music, or videos folder in the user’s profile."

First, let's find the Readme file. 

`-Recurse`
Recursively search through every folder

`-File`
Return only files, not directories.

`-Include`
We only want to include this specific search. Also, we can utilize a `*` aka wildcard to assist with the case sensitivity or how the file might be named. Essentially we're just searching for any file with the string "readme" in its name.

```console
PS C:\users\century7\desktop> cd ..
PS C:\users\century7> Get-ChildItem -Recurse -File -Include *readme*


    Directory: C:\users\century7\Downloads


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        8/30/2018   3:29 AM              7 Readme.txt


PS C:\users\century7>
```

Now we can get the contents of that file.

```console
PS C:\users\century7> cat .\Downloads\Readme.txt
7points
```

Or we can do it all as a one-liner.

```console
PS C:\users\century7> Get-ChildItem -Recurse -Include *readme* -File | cat
7points
```

**Username:** century8

**Password:** 7points

---

## Century 8 -> 9
[https://underthewire.tech/century-8](https://underthewire.tech/century-8)

> The password for Century9 is the number of unique entries within the file on the desktop.

We'll be doing a lot of `|` aka piping. Essentially this is just taking the output of one command and parsing it to another command to be manipulated or interacted with in some way.

- `Get-Content`
This cmdlet is essentially the equivalent of `cat`. So we're outputting the contents of `unique.txt`.

- `Sort-Object`
Now we can sort the entries within the text file.

- `Get-Unique`
Returns only the "unique" entries.

- `.count`
Returns the number of entries.

```console
PS C:\users\century8\desktop> (Get-Content .\unique.txt | Sort-Object | Get-Unique).count
696
```

**Username:** century9

**Password:** 696

---

## Century 9 -> 10
[https://underthewire.tech/century-9](https://underthewire.tech/century-9)

> "The password for Century10 is the 161st word within the file on the desktop."

In this level, we have more output manipulation. First, let's output the contents of the file.

```console
PS C:\users\century9\desktop> Get-Content .\Word_File.txt
larceny epibole ampliate trecentos psychotoxic sybarism shatterwit cartilaginification crenulation splenification freespac untragicalness
renovater smirch historism tymbal nonobjectivist protestive octobass crownal retrorenal activation ascocarp clawing unaccordingly strontia
nite refutatory reline unsubmersible unstuffy asynergia asha rejunction spiritrompe preestimates papabot postcoital forbearantly epistoliz
e corkwood rasers logicized rearrange rectigraph signposts prothrombin headkerchief upholden oversocialize semiperimeter hackbuteer tickli
sh brachiated atheneum naegait engrasp palaeoconcha deminudity tragions curteous stratal swandown succinylcholine swooners caskanet irresp
ectability flocculant palatefulness thalamocoele maleate tittivate eustachium etudes loppering fidos flayers murrion uninduced numbedness
nincompoopish compressors cassoulet protura fagopyrismus sesquibasic paxwaxes grievous remonstrator fulvid rotatoria ultraconservatives po
stcards hairdresser wagnerianism mistreats nefarious winberry usherance conductility yearner uranostaphylorrhaphy rehabilitator agrapha ju
nglegym emanant coy gaelicist parallelogram wealdsman objurgator tapeline amay psalterer eleostearate mainprise overdyeing dowly coronado
localed weasellike scattergram tocological disproportionation archicerebrum glazement zugtierlaster sleepwort yabber tenontodynia laevulos
e walkaway readept literally weinmannia englut caulopteris schellingian thiamid suberizes bistorta quinetum woolulose jaculiferous trestle
work unoriginativeness kua uncontemptibleness unconcernedly taryard escapologist traumata chlorochrous exocolitis dysgnosia steadfastness
keratoleukoma inordinate sacahuiste trippler intoxicatively pierid nonapplicabness patinas rabific scandaliser waggel reauthenticate sufei
sm lairds cookee bragget ledgering perceptual chomper obscurities merino ganguela unproposed epulis loppard ignoblesse carrotage heartbrok
enly unfusibness degenerate lacunae cirrocumulus knightlike overwhelmingness oxyrrhyncha capitalizations dimethylamine uninucleate syndicship graspable tropophil telchines abaiser overclement pursive
```

If we try to get a count what happens?

```console
PS C:\users\century9\desktop> (Get-Content .\Word_File.txt).count
1
```

Remember `.count` only returns the number of lines in a file. In this case, we have several strings on one line. So we need a way of splitting these strings onto separate lines.

We can do this by specifying a delimiter. A delimiter is usually a specified character to separate different pieces of data. In this case, a space is our delimiter.

```console
Get-Content .\Word_File.txt -Delimiter ' '
...
cirrocumulus
knightlike
overwhelmingness
oxyrrhyncha
capitalizations
dimethylamine
uninucleate
syndicship
graspable
tropophil
telchines
abaiser
overclement
pursive
```

Our delimeter is working as it output the words on separate lines. Let's try to get a count now.

```console
PS C:\users\century9\desktop> (Get-Content .\Word_File.txt -Delimiter ' ').count
200
```

Aha! Now we can see the file has a total of 200 words. Let's specify an index to grab the 161st word in the file. 

```console
PS C:\users\century9\desktop> Get-Content .\Word_File.txt -Delimiter ' ' | Select-Object -Index 160
pierid
```

Why did we put 160 instead of 161? One key thing to note is that computers don't count starting from 1. They count starting from 0. So we counting from 0 we conclude that the word at index 160 is actually the 161st word.

**Username:** century10

**Password:** pierid

---

## Century 10 -> 11
[https://underthewire.tech/century-10](https://underthewire.tech/century-10)

> "The password for Century11 is the 10th and 8th word of the Windows Update service description combined PLUS the name of the file on the desktop."

Several ways you could do this level but I chose to do this purely programmatically. 

```console
PS C:\Users\century10\Documents> Get-Service -DisplayName *update*

Status   Name               DisplayName
------   ----               -----------
Stopped  wuauserv           Windows Update

PS C:\Users\century10\Documents> Get-Service -Name "Windows Update" | Select-Object *

Name                : wuauserv
RequiredServices    : {rpcss}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : False
DisplayName         : Windows Update
DependentServices   : {}
MachineName         : .
ServiceName         : wuauserv
ServicesDependedOn  : {rpcss}
ServiceHandle       :
Status              : Stopped
ServiceType         : Win32ShareProcess
StartType           : Manual
Site                :
Container           :

PS C:\Users\century10\Documents> Get-WMIObject -Class Win32_Service -Filter  "Name='wuauserv'"  | Select-Object *

PSComputerName          : CENTURY
Name                    : wuauserv
Status                  : OK
ExitCode                : 0
DesktopInteract         : False
ErrorControl            : Normal
PathName                : C:\Windows\system32\svchost.exe -k netsvcs
ServiceType             : Share Process
StartMode               : Manual
[..]
Description             : Enables the detection, download, and installation of updates for Windows and other programs. If this service is
                          disabled, users of this computer will not be able to use Windows Update or its automatic updating feature, and programs
                          will not be able to use the Windows Update Agent (WUA) API.
DisplayName             : Windows Update
[..]
```

Expand the "Description" property.

```console
PS C:\users\century10\desktop> Get-WmiObject -Class Win32_Service -Filter "Name='wuauserv'" | Select-Object -ExpandProperty Description
Enables the detection, download, and installation of updates for Windows and other programs. If this service is disabled, users of this computer will not be able to use Windows Update or its automatic updating feature, and programs will not be able to use the Windows Update Agent (WUA) API.
```

Lint the data to the format we need.

```console
PS C:\users\century10\desktop> $desc = (Get-WmiObject -Class Win32_Service -Filter "Name='wuauserv'" | Select-Object -ExpandProperty Description).ToLower().Split()
```

A bit of string splicing/indexing to grab the words as well as the name of the file on the desktop we need.

```console
PS C:\users\century10\desktop> $word10 = $desc | Select-Object -Index 9
PS C:\users\century10\desktop> $word8 = $desc | Select-Object -Index 7
PS C:\users\century10\desktop> $desktopfilename = Get-ChildItem -Path C:\Users\century10\Desktop -Name
```

Finally, putting it all together now thus returning the password for Century11.

```console
PS C:\users\century10\desktop> $pass = $word10 + $word8 + $desktopfilename
PS C:\users\century10\desktop> $pass
windowsupdates110
```

**Username:** century11

**Password:** windowsupdates110

---

## Century 11 -> 12
[https://underthewire.tech/century-11](https://underthewire.tech/century-11)

> "The password for Century12 is the name of the hidden file within the contacts, desktop, documents, downloads, favorites, music, or videos folder in the user’s profile."

Fairly simple level. We just specify an array of folders we want to search with the `-Hidden` parameter. I added a few other parameters to exclude any false positives such as the `desktop.ini` file.

```console
PS C:\users\century11\desktop> cd ..
PS C:\users\century11> Get-Childitem -Path Contacts,Desktop,Documents,Downloads,Favorites,Music,Videos -File -Hidden -Recurse -Exclude desktop.ini -ErrorAction SilentlyContinue


    Directory: C:\users\century11\Downloads


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
--rh--        8/30/2018   3:34 AM             30 secret_sauce


PS C:\users\century11>
```

**Username:** century12

**Password:** secret_sauce

---

## Century 12 -> 13
[https://underthewire.tech/century-12](https://underthewire.tech/century-12)

> "The password for Century13 is the description of the computer designated as a Domain Controller within this domain PLUS the name of the file on the desktop."

Get the file name on the Desktop.

```console
PS C:\users\century12\desktop> (Get-ChildItem -Path . -Name)
_things
```

Get the hostname of the Domain Controller using `Get-ADDomainController.

```console
PS C:\users\century12\desktop> Get-ADDomainController


ComputerObjectDN           : CN=UTW,OU=Domain Controllers,DC=underthewire,DC=tech
DefaultPartition           : DC=underthewire,DC=tech
Domain                     : underthewire.tech
Enabled                    : True
Forest                     : underthewire.tech
HostName                   : utw.underthewire.tech
InvocationId               : 09ee1897-2210-4ac9-989d-e19b4241e9c6
IPv4Address                : 192.99.167.156
IPv6Address                :
IsGlobalCatalog            : True
IsReadOnly                 : False
LdapPort                   : 389
Name                       : UTW
NTDSSettingsObjectDN       : CN=NTDS
                             Settings,CN=UTW,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=underthewire,DC=tech
OperatingSystem            : Windows Server 2016 Standard
OperatingSystemHotfix      :
OperatingSystemServicePack :
OperatingSystemVersion     : 10.0 (14393)
OperationMasterRoles       : {SchemaMaster, DomainNamingMaster, PDCEmulator, RIDMaster...}
Partitions                 : {DC=ForestDnsZones,DC=underthewire,DC=tech, DC=DomainDnsZones,DC=underthewire,DC=tech,
                             CN=Schema,CN=Configuration,DC=underthewire,DC=tech, CN=Configuration,DC=underthewire,DC=tech...}
ServerObjectDN             : CN=UTW,CN=Servers,CN=Default-First-Site-Name,CN=Sites,CN=Configuration,DC=underthewire,DC=tech
ServerObjectGuid           : df17c8a3-dd76-438b-8ddf-b7ad3e624618
Site                       : Default-First-Site-Name
SslPort                    : 636
```

or...

```
PS C:\users\century12\desktop> hostname
utw
```

Based up the DC's name we can examine the Description property using `Get-ADComputer`

```console
PS C:\users\century12\desktop> Get-ADComputer UTW -Property Description


Description       : i_authenticate
DistinguishedName : CN=UTW,OU=Domain Controllers,DC=underthewire,DC=tech
DNSHostName       : utw.underthewire.tech
Enabled           : True
Name              : UTW
ObjectClass       : computer
ObjectGUID        : 5ca56844-bb73-4234-ac85-eed2d0d01a2e
SamAccountName    : UTW$
SID               : S-1-5-21-758131494-606461608-3556270690-1000
UserPrincipalName :
```

Select the description of the UTW computer object.

```console
PS C:\users\century12\desktop> (get-adcomputer UTW -Properties * | Select-Object -ExpandProperty Description)
i_authenticate
```

Putting it all together.

```console
PS C:\users\century12\desktop> (get-adcomputer UTW -Properties * | Select-Object -ExpandProperty Description) + (Get-ChildItem -Path . -Name)
i_authenticate_things
PS C:\users\century12\desktop>
```

**Username:** century13

**Password:** i_authenticate_things

---

## Century 13 -> 14
[https://underthewire.tech/century-13](https://underthewire.tech/century-13)

> "The password for Century14 is the number of words within the file on the desktop."

More file parsing. Two methods we can use here.

**Method 1**

```console
PS C:\users\century13\desktop> Get-Content .\countmywords | Measure-Object -Word

Lines Words Characters Property
----- ----- ---------- --------
        755
```

**Method 2**

```console
PS C:\users\century13\desktop> (Get-Content .\countmywords).Split(' ').Count
755
```

**Username:** century14

**Password:** 755

---

## Century 14 -> 15
[https://underthewire.tech/century-14](https://underthewire.tech/century-14)

> "The password for Century15 is the number of times the word “polo” appears within the file on the desktop."

Final challenge! In this one, we're doing a little bit of pattern matching.

```console
PS C:\users\century14\desktop> (Get-Content .\countpolos -Delimiter  ' ' | Select-String -Pattern '\bpolo\b').count
153
```

I originally had an issue with the command matching every occurrence of the string 'polo' thus returning words like antro**polo**gic or car**polo**gy which I didn't need. Using the `\b` anchor specifies that the match must occur between the boundary.

`\b` matches a word boundary. See [the documentation](https://docs.microsoft.com/en-us/dotnet/standard/base-types/anchors-in-regular-expressions?redirectedfrom=MSDN#word-boundary-b).

**Username:** century15

**Password:** 153

---

I hope you enjoyed this challenge! I feel like it definitely strengthened my PowerShell skills and I'm looking forward to doing more of them in the future!
