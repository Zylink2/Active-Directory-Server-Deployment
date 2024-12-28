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


