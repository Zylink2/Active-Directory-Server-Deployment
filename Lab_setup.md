# Prior to Set up
Download ISO files for Window Server 2022 and Window Client from Microsoft website

![image](https://github.com/user-attachments/assets/dcd33b4b-195c-4274-a8d4-ae6ea9fa42ac)

# Sum up Accounts we have created (This part is intended to be a recap after all steps completed)
## Domain Account
Domain admin (Built-in) - Administrator:P@$$w0rd!<br>
Domain admin - tstark:Password12345!<br>
SQL Service w/ admin privilege - SQLService:MYpassword123#<br>
Low level user - fcastle:Password1 (have admin privilege on both machines since it got added into local admin group for both machines)<br>
Low level user - pparker:Password2 (have admin privilege on just SPIDERMAN machine since it got added into local admin group on that machine)<br>

## Local Account
Administrator (both machines) - administrator:Password1!
THEPUNISHER - frankcastle:Password1<br>
SPIDERMAN - peterparker:Password1<br>

# Set up DC

After run an inial set up for Windows server 2022, we will set admin password to something weak like: **P@$$w0rd!**

![image](https://github.com/user-attachments/assets/7b560701-b761-4ba0-ba82-22dd2261b394)

Change the PC name to HYDRA-DC (I like Marvel theme of it)

![image](https://github.com/user-attachments/assets/619e85b2-ba11-404a-ab1b-f3114095bd6b)

This server is not a DC yet so we want to set this into DC, in Server Manager by proceeding with these steps

![image](https://github.com/user-attachments/assets/839f27d0-c282-495c-81c1-66908e425847)

![image](https://github.com/user-attachments/assets/007cfb84-159b-4ac8-9d68-16c013ae070b)

![image](https://github.com/user-attachments/assets/a1e41bfc-2fce-446c-a70c-1527b8cd67f4)

we'll add ADDS from this options

![image](https://github.com/user-attachments/assets/835d84c9-1374-4061-9d89-69e573570045)

![image](https://github.com/user-attachments/assets/1a719f48-1955-45b8-9169-d32d8ba86734)

Once the installation is done we need to promote this server into Domain Controller

![image](https://github.com/user-attachments/assets/f94d21a5-9bd9-4e69-89b7-31197ba9fbcc)

Since we don't have any domain yet we'll create a new forest called 'Marvel.local'

![image](https://github.com/user-attachments/assets/701e0f03-028a-49e6-a75b-e046d190bade)

Create a password the same as admin password: **P@$$w0rd!**

![image](https://github.com/user-attachments/assets/3c7c34ec-726c-493d-af1a-0323193e56c0)

we get the NetBIOS domain name now

![image](https://github.com/user-attachments/assets/5b2d22e8-6e15-4fae-ac12-081c205e21ab)

Check for any prerequistite then install

![image](https://github.com/user-attachments/assets/a0251c05-0af5-4c72-bdc8-f9a4576d978e)

![image](https://github.com/user-attachments/assets/269226a6-5450-4c35-91e1-0828103fb9a5)

We got the domain account login prompt here when restart

Now we're done with setting up the DC let's move on to setting the certificate signing for internal services

# Set up Certificate Service
We need to set this up in order to indentify identity of the DC and also for LDAPS for the security's sake

Install the certificate service

![image](https://github.com/user-attachments/assets/ae9a6a32-e9d2-44be-bf73-9428f283d73f)

We need to configure AD service

![image](https://github.com/user-attachments/assets/84810d6b-8045-47e5-84ff-d40c821dc5b2)

![image](https://github.com/user-attachments/assets/cfa146d8-1fd4-412e-8318-13f4ef3a5c80)

![image](https://github.com/user-attachments/assets/99b32355-ab50-43ed-a2fd-8d227ab38735)

![image](https://github.com/user-attachments/assets/4b3e1a6c-ec4b-478e-b32a-c9130a6d9f50)

![image](https://github.com/user-attachments/assets/864212ec-e542-4f19-8785-c83666658b20)

we don't have any private key yet so we need to create a new one with SHA256 as a default and validity to 99 years so it doesn't get expired

![image](https://github.com/user-attachments/assets/fbce75df-7dea-459a-a8c2-d3933c8d3021)

![image](https://github.com/user-attachments/assets/b0c0bdc9-50d7-4dfa-b140-8cf000281d29)

![image](https://github.com/user-attachments/assets/5378d5bf-a534-496f-958f-69ce988a272d)

![image](https://github.com/user-attachments/assets/53d04926-f787-4599-ba21-2dbdee2cc2ce)

Then hit configure to successfully configure the service

![image](https://github.com/user-attachments/assets/1ee850a2-3232-4fd6-bfe2-35fb34dbd78f)

# Set up Client Machine
we will set up both of the machine using same iso file. One will be THEPUNISHER and another for SPIDERMAN
after the initial set up we can use domain join account to sign up both of the machines instead of microsoft account

![image](https://github.com/user-attachments/assets/86967b6b-6a4c-446f-9a8b-4f4893a6cd9e)

The name of local account of PUNISHER machine is going to be "frankcastle"

![image](https://github.com/user-attachments/assets/31e8f3ae-49bd-4c99-9214-80bd42a31066)

As for SPIDERMAN, we'll be using "peterparker"

![image](https://github.com/user-attachments/assets/56dbfd8a-e81d-4e25-9938-abae0422fcda)

Both machines will use same password: **Password1**

![image](https://github.com/user-attachments/assets/9d0d0a92-750e-4c30-a4d0-8ebc16259837)

For all security questions for both machines can be anything and all of the answers we're setting is going to be just "bob"

![image](https://github.com/user-attachments/assets/fc3066f2-6198-4fab-bcdb-8a05b68bb21b)

select no for everything since we don't want to expose any of our machine information to the internet

![image](https://github.com/user-attachments/assets/d5cb3845-675a-4520-9342-26ef2e1cefeb)

After the installation for both machines completed, change the PC name to each of the name we assigned earlier

![image](https://github.com/user-attachments/assets/9dcc0879-5ccc-4710-927a-bc97ddce5d62)

![image](https://github.com/user-attachments/assets/fc72122d-a10c-433a-a314-0200cce48fe4)

And we're done with setting up machine

# Set up Domain Users
Let's set up our users in the domain

![image](https://github.com/user-attachments/assets/50491fe0-a5c0-4267-b624-8d83d73b6fab)

The users here is a little bit overwhelmed. We can create OU of group to seperate the security group from users

![image](https://github.com/user-attachments/assets/2b903555-d972-40f3-b6b8-b3cfd5f3e1d9)

![image](https://github.com/user-attachments/assets/cb3fa96e-9def-4b52-9b16-a527537f161b)

![image](https://github.com/user-attachments/assets/d62eb536-24bc-418f-b544-78126793468d)

![image](https://github.com/user-attachments/assets/ae0676b2-728f-45fc-b1a6-c49a2d7304e9)

![image](https://github.com/user-attachments/assets/740d18a0-9cec-45cc-af3e-445a3825e486)

Now they are seperated! Next, we will create another admin copy from the existing admin to get all the privileges that has already been configured on that account

![image](https://github.com/user-attachments/assets/2afc2913-bf97-4267-b34a-905feeb9bd9c)

we'll set name convention for the logon as "tstark" (first letter of the first name + last name)

![image](https://github.com/user-attachments/assets/d9398a7c-32de-4f20-a24a-9cf919b23c28)

The password will be:** Password12345!**

![image](https://github.com/user-attachments/assets/a9498b61-3a36-4dfd-8ff3-b8a8d2abbcf4)

Next we'll create SQL service account copy from the built-in admin again

![image](https://github.com/user-attachments/assets/4591121f-7a83-4fbb-a0c1-c2d820a89d5e)

The password: **MYpassword123#** and that should be it

Let's double click into SQL account and set the description with the password since a lot of domain admin always do this thinking only them can see that but all users can see it (to demonstrate in the later attacks that how insecure this is)

![image](https://github.com/user-attachments/assets/ec9d5cb6-4ba5-4c6c-9eab-3036eeaa0330)

Now just create the rest of 2 low level domain users (right click on blank space)

![image](https://github.com/user-attachments/assets/7c152004-3c5c-4077-929b-263337381de2)

![image](https://github.com/user-attachments/assets/fb09d3ee-a026-406e-8b63-dd785de234c9)

Password: **Password1**

![image](https://github.com/user-attachments/assets/5e1b5d6f-711c-40e6-a8c8-102dacfbddd7)

Password: **Password2**
Both account should set the password to never expire

# Set up File Shares (For Later Attacks)

![image](https://github.com/user-attachments/assets/e261b4c2-cae5-4333-a891-93f15b3376fd)

![image](https://github.com/user-attachments/assets/ee62b9ba-7a8e-4eb1-b9f9-236c7eb24bcb)

![image](https://github.com/user-attachments/assets/5223de17-2594-4a6e-8497-6e6d5813c078)

Set the sharename to "hackme"

![image](https://github.com/user-attachments/assets/3e81713e-6afe-4462-98bd-f199372d91fc)

![image](https://github.com/user-attachments/assets/c7b807d9-9494-4b43-a693-a989a1eb1457)

![image](https://github.com/user-attachments/assets/201861c6-127b-4da8-ba42-c52071b98f4d)

![image](https://github.com/user-attachments/assets/3cab3873-beb7-4f3d-bfc0-7915b2d0f309)

![image](https://github.com/user-attachments/assets/96627933-3134-4e61-b4ac-64f219975d24)

Let's set up a file link. Open cmd as admin and do the setspn (Set service principal name) for SQL Service

```
setspn -a HYDRA-DC/SQLService.MARVEL.local:60111 MARVEL\SQLService
setspn -T MARVEL.local -Q */*
```

![image](https://github.com/user-attachments/assets/9ab0050d-144e-431c-932e-45776f4d1260)

Query the spn to make sure we successfully created it

![image](https://github.com/user-attachments/assets/fa3a919c-24ec-451a-91a0-2d9ce8790405)

![image](https://github.com/user-attachments/assets/bd92b6c3-adc2-4f97-9dd2-006b7cd2feea)

# Set up Grouop Policy

![image](https://github.com/user-attachments/assets/11e6e270-fafc-45f2-9726-5c5b7cc03734)

Create a Disable Windows Defender Policy for the entire domain

![image](https://github.com/user-attachments/assets/381905cc-53ea-4a30-9348-60d7e13a1176)

![image](https://github.com/user-attachments/assets/b925689f-ef02-483d-a691-8786dfbe30fa)

We need to disable windows defender in our environment so that we could run all of the attacks

![image](https://github.com/user-attachments/assets/e8f90d55-be5f-4ed1-ba56-67e8381dcd57)

![image](https://github.com/user-attachments/assets/9b7b9c27-9bc9-4c5e-b5e4-0625667c657b)

Double click into it and select enable -> apply -> ok
Then we should be ready to enforce the policy

![image](https://github.com/user-attachments/assets/34e78ddb-deab-476b-8502-4feb33d667a1)

Everytime users or computer joins a domain, it will get the policy updated

# Set up DC's Static IP Address

Now, we have this dynamic IP address

![image](https://github.com/user-attachments/assets/626371e8-d655-4545-8031-aa1638a2826b)

![image](https://github.com/user-attachments/assets/1411b3cd-98b4-43b8-bdc7-bb2960209283)

double click into an adapter

![image](https://github.com/user-attachments/assets/7585aedd-6693-44a4-a480-9372a298bd80)

We can use the IP that's the same as the dynamic address for both machine's IP and gateway's IP

![image](https://github.com/user-attachments/assets/03c63637-180e-46bb-865f-64265bcd593e)

After this, the DC won't have any access to the internet but that's fine since we'll just use LAN network

# Joining Our Machines to the Domain 

we will add the DC ip as DNS server since we want it to be our DNS server for both frank and peter client machine

![image](https://github.com/user-attachments/assets/f884ef5d-824e-48ec-a6f4-6b3ed9d3d3b6)

![image](https://github.com/user-attachments/assets/c044aa72-137d-4460-b0b2-f65058f92b12)

Enter the domain we want to join which is MARVEL.local

![image](https://github.com/user-attachments/assets/39331374-9807-4d28-a2cb-422c21bc57ae)

The window prompts for credential of DC should pop up "administrator:P@$$w0rd!" which is an admin credential on DC

![image](https://github.com/user-attachments/assets/5bd974a5-e8cc-4207-b935-a76a8a48d4bc)

After that, restart both machines

We can come back to AD and check if those 2 machines has joined or not

![image](https://github.com/user-attachments/assets/99fbc8f5-29ee-4fc9-a0e0-dc4a32f8f7a1)

Both are now joined to DC

We will add local admin to THEPUNISHER machine here

![image](https://github.com/user-attachments/assets/f7f5ce0a-c0b5-4c29-b423-1fc6ab573bf7)

Set password to: **Password1!** and uncheck Account is Disabled  

![image](https://github.com/user-attachments/assets/79a6d72d-10a8-46cc-9592-53fb34ea5f71)

We will add another user into Administrator group

![image](https://github.com/user-attachments/assets/aeee4dc4-9131-4703-8ca6-ab4621ec96bf)

Make sure to check network folder that the network discovery is turned on

![image](https://github.com/user-attachments/assets/df0f06cd-0bf9-4d35-8eab-14f663792829)

![image](https://github.com/user-attachments/assets/8851ea9a-46c8-4a06-8048-5bf67fceb407)

We should be able to see HYDRA-DC shared folder

![image](https://github.com/user-attachments/assets/ab53fbc2-0caa-4832-8950-33059f209541)

Let's do the same thing with SPIDERMAN machine, the same password as well: **Password1!**, but instead of adding 1 admin user we will add 2 which includes frank and peter

Now, logout of SPIDERMAN machine as admin and try to sign in with local peterparker account with the local user password: Password1

![image](https://github.com/user-attachments/assets/c3997407-fc35-4482-b45a-1ce800dfa6be)

(we need to specify to the machine that we will sign in as local account that's why the .\ is needed or else the machine will think that we want to sign in using domain account)

we want to map the share to this machine

![image](https://github.com/user-attachments/assets/8943d3e7-875f-4c1b-859b-2d301542fd31)

![image](https://github.com/user-attachments/assets/f2c2f355-6b53-453b-806d-0eca3c5ca5b0)

we want to use domain admin creds to sign in "admininstrator:P@$$w0rd1"

![image](https://github.com/user-attachments/assets/9b6301e1-d452-414c-b173-163cd9f88d61)

we should see the share drive on the machine here which is login under DC admin account 

![image](https://github.com/user-attachments/assets/31a7b093-cdd0-47a5-88cb-b9adcec1ad84)

And we are all set for the attack!
