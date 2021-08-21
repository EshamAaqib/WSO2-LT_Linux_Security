# WSO2_Linux_Security_Assignment


## Operating Systems used
1. Ubuntu
2. Kali Linux

## IP Addresses 
1. Web Server - 192.168.1.230
2. Victim     - 192.168.1.233
3. Hacker     - 192.168.1.20


## 1. Apply controls to protect from DDOS attacks

### Installing Apache Web Server

```
sudo apt update
sudo apt install apache2
```

### After installing I went ahead and uploaded a sample website to the /var/www folder and created a new config file in /etc/apache3/sites-available/

```
nano /etc/apache3/sites-available/gci.conf
sudo a2ensite gci.conf
systemctl reload apache2
```

###### After following the above steps you should have a working simple website.

### Afterwards I ran a SYN Flood Attack by spoofing my IP to the victims IP address. This is the command that I executed

```
hping3 -a 192.168.1.233 192.168.1.230 -S --flood -V -p 80
```
###### 192.168.1.233 is the victim's IP and 192.168.1.230 is the web server's IP address.

###### Initiating SYN Flood attack (Spoofed IP)
![image](https://user-images.githubusercontent.com/75664650/130322699-d6d7c32d-9233-4b9f-8226-2fbefc2c2cf6.png)

###### Tcp Dump (Hacker)

![image](https://user-images.githubusercontent.com/75664650/130322740-2f7bef27-9b62-4c9a-8b9d-468460716e4a.png)

###### TCP Dump (Web Server)

![image](https://user-images.githubusercontent.com/75664650/130322758-41931a42-86b4-4337-9347-53461d25be5b.png)

###### TCP Dump (Victim)

![image](https://user-images.githubusercontent.com/75664650/130322786-4a15d731-8a13-4d61-befb-caaa0f2d5d26.png)

### After running the SYN Flood Attack the load time of the wepbage was extremely slow and sometimes it was not even accessible. 

### To avoid SYN Flood Attacks I went ahead and configured iptables. This is the command I executed

```
sudo iptables -I INPUT 1 -p tcp --syn -m hashlimit --hashlimit 1/s --hashlimit-burst 3 --hashlimit-mode srcip --hashlimit-srcmask 32 --hashlimit-name synattack -j ACCEPT
sudo iptables -I INPUT 2  -p tcp --syn -j DROP
sudo iptables -I INPUT 1 -p tcp --syn -m hashlimit --hashlimit 1/s --hashlimit-burst 3 --hashlimit-mode srcip --hashlimit-srcmask 32 --hashlimit-name synattack -j LOG --log-prefix '** SYN ATTACK DETECTED **'
```
















































