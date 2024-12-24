# Active-Directory-Server-Deployment

## Objective

The project aimed to build AD DS which can be used to establish a controlled environment for simulating and detecting cyber attacks. The primary focus was to understanding components of Active Directory, generating test telemetry to mimic real-world attack scenarios. This hands-on experience was designed to deepen understanding of network security, attack patterns, and defensive strategies.

### Skills Learned

- Better understanding in Active Directory in the internal network.
- Engagement with Group policies.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tool Used

- Oracle Virtual Box

## <a href='https://drive.google.com/file/d/1Ygpqy3n8Vr6QtE4SZk6iUZkbiRU_WROF/view?usp=sharing'>Details</a>
## Prior to set up
Download ISO files for Window Server 2022 and Window Client from Microsoft website

![image](https://github.com/user-attachments/assets/dcd33b4b-195c-4274-a8d4-ae6ea9fa42ac)

## Set up DC

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

## Set up Certificate services
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

# Set up user machine
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
