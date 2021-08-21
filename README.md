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
```

###### After adding the above iptables I went ahead and initiated the SYN Flood attack on my Kali Machine. At that point the victim was unable to access the server but the other were able to access it with no issues. 

###### Web server can be accessed via a different machine

![Untitled](https://user-images.githubusercontent.com/75664650/130328959-f5b96ae9-d1a6-468a-91a1-1bbeba5f4dde.png)


###### Web server cannot be accessed through the victim

![image](https://user-images.githubusercontent.com/75664650/130328964-c9375c78-b3c4-4e2d-81dd-4e2f43f051b9.png)


### Also I enabled iptable logging so we can log the source ip and destination ip when the set rule is triggered.

```
sudo iptables -I INPUT 1 -p tcp --syn -m hashlimit --hashlimit 1/s --hashlimit-burst 3 --hashlimit-mode srcip --hashlimit-srcmask 32 --hashlimit-name synattack -j LOG --log-prefix '** SYN ATTACK DETECTED **'
```

###### Iptable log files are generated by the kernel and it can be accessed via the kernel log file. So I wanted to save the iptable logs to a seperate file. I first edited the rsyslog.conf file located in /etc and added the followng line to it

```
kern.warning /var/log/iptables.log
```

###### Then I restarted rsyslog service using the following command

```
service rsyslog restart
```

###### Screenshot of the iptable logs 

![image](https://user-images.githubusercontent.com/75664650/130329002-64f83391-47d0-4d31-8e2b-490d992b82df.png)





















































