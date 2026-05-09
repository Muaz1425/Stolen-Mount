
# Stolen Mount Room

Writeup about TryHackme Stolen Mount Challenge Room

[Diffuculty: Easy]
[Blue Team]

https://tryhackme.com/room/hfb1stolenmount




## Scenario

![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountScenario.png)

The question require us to analyse PCAP file for NFS server packets that being exfiltrate.


Before start doing the question, we need to understand what NFS Server: 


```bash
- NFS server is distributed file system protocol that lets other machine to access through network as if it locally.

- The way NFS server works is that NFS mounts a remote directory from server directly into clients file system tree.

- NFS server use port 2049/TCP and 111/TCP depending on its version.

- NFSv3 and lower send file data in plaintext over the network, due to that others can see the file data that being transferred in PCAP.

- Meanwhile NFSv4 do have option for encryption traffic but that doesn't mean encryption being enable. We can check whether the traffic is enable encryption or not.
```


## Walkthrough

![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountPacket1.png)

From the NFS traffic, we can see the (Program Version: 4) shows that the packet is NFSv4.


![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountPacket2.png)

In Verifier section, *Flavor* shows whether the NFS packets haave encryption enabled or not.

Flavor type:
```bash
Auth_Null = no auth
Auth_sys = IP/UID trust only
krb5 = Kerberos - auth only
krb5i = Kerberos + integrity
krb5p = Kerberos + encryption
```
From the Screenshot above, we can see the Flavor is Auth_Null which mean the traffic is not encryption.


![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountHexDump.png)

Follow the tcp stream of NFS packet so that we can see the entire conversation, and change the mode into "Show as: Hexdump" so we can copy entire conversation into the CyberChef to decode it.

We copy the entire conversation from client to server to be more efficient than copy part by part. Then paste it in CyberChef to decode it using "From Hexdump" since from PCAP is Hexdump and "Extract Files" since we want to get files being send out.


![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountCyberchef.png)

From the entire conversation being deocde we get to extract two files. One of the files is corrupted so it leave us with file (extracted_at_0x8e64.zip) but it require password to read it.


![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountHexDumpPassword.png)

To find the password, we need to look through the tcp stream again to find anything that standout as password. From that we go to the packet that contain the password and copy the string that have password as hex string.


![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountPassword.png)

Copy the password strings.


![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountCyberchefPassword.png)

Paste the password string inside the CyberChef and we see that password is encode using md5.


![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountDecodePassword.png)

Take the password and decode it using md5 and we see the password is "avengers"


![App Screenshot](https://github.com/Muaz1425/Stolen-Mount/blob/main/Images/StolenMountFlag.png)

Once we unlocked the file, we can see the png contain qrcode. After we scan, we get the flag.

With this, we can completed the room.
