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

## Set up Certificate

