

## Connect the ZAP Desktop Lightning wallet to the RaspiBlitz

The desktop app ZAP (https://github.com/LN-Zap/zap-desktop)
) is a cross platform Lightning Network wallet focused on user experience and ease of use.

Download ZAP for your operating sytem:
https://github.com/LN-Zap/zap-desktop/releases  
Install instructions: https://github.com/LN-Zap/zap-desktop#install


### Preparation on the Pi

* Allow connections to the RaspiBolt from your LAN. Check what your LAN IP address is starting with eg. 192.168.0 or 192.168.1 and use the address accordingly. Ending with .0/24 will allow all IP addresses from that network.
    `$ sudo nano /home/bitcoin/.lnd/lnd.conf`  

    Add the following line to the section `[Application Options]`:  
  ```tlsextraip=192.168.0.0/24```
  
* Delete tls.cert (restarting LND will recreate it):  
    `$ sudo rm /home/bitcoin/.lnd/tls.*`

* Restart LND :  
  `$ sudo systemctl restart lnd`  
(If LND doesn't start, restart the Raspberry Pi with: `$ sudo shutdown -r now`)

* Copy the new tls.cert to user "admin", as it is needed for lncli:  
    `$ sudo cp /home/bitcoin/.lnd/tls.cert /home/admin/.lnd`

* Unlock wallet  
  `$ lncli unlock` 

* Allow the ufw firewall to listen on 10009 from the LAN:  
  `$ sudo ufw allow from 192.168.0.0/24 to any port 10009 comment 'allow LND grpc from local LAN'`

 * restart and check the firewall:  
  `$ sudo ufw enable`  
  `$ sudo ufw status`


### On your Linux desktop terminal:  

* Copy the tls.cert to your home directory:  
  `$ scp admin@your.RaspiBolt.LAN.IP:/home/admin/.lnd/tls.cert ~/`

* Copy the admin.macaroon to your home directory:  
`$ scp root@your.RaspiBolt.LAN.IP:/home/bitcoin/.lnd/data/chain/bitcoin/mainnet/admin.macaroon ~/`

### On a Windows computer:

Follow the first three steps in https://github.com/Stadicus/guides/blob/master/raspibolt/raspibolt_50_mainnet.md#temporarily-enable-password-login like this:
* On the Raspberry Pi, as user "admin", edit the SSH config file and put a # in front of "PasswordAuthentication no" to disable the whole line. Save and exit.  
`$ sudo nano /etc/ssh/sshd_config`  
`# PasswordAuthentication no`

* Restart the SSH daemon.  
`$ sudo systemctl restart ssh`

* On your Windows computer, start WinSCP. Connect to your Raspberry Pi with the same credentials used with Putty. For example:

Host name:  
`192.168.0.22`  
Port number:  
`22`  
Use user "bitcoin". User name:  
`bitcoin`  
And your master password:  
`PASSWORD_A`

* Navigate to `/mnt/hdd/lnd`and download `tls.cert` to a folder on your windows computer. Then naivgate to `/mnt/hdd/lnd/data/chain/bitcoin/mainnet/` and download `admin.macaroon`. 

* On the Raspberry Pi, dissable password login again. As user "admin", remove the `#` in front of "PasswordAuthentication no" to enable the line. Save and exit.    
`$ sudo nano /etc/ssh/sshd_config`  
`PasswordAuthentication no`

### Configure ZAP

* Start the app and select:  
```Connect your own node```

![](zap1.png)


* Fill in the next screen:  
`your.RaspiBolt.LAN.IP:10009`  
`~/tls.cert`  
`~/admin.macaroon`  

![](zap2.png)

* Confirm the settings on the following screen and you are done!

