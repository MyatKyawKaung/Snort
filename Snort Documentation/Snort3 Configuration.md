<h2 align="left"> Configuring Network Cards </h2>

### Create a systemD service to change network settings

Firstly determine the name(s) of the interfaces you will have snort listen on
```
ip a
```
![image01](https://github.com/user-attachments/assets/7afd21be-aeeb-4da9-aac4-414393b2ff26)

After identifying the network interface that Snort is listening on, check the status of large-receive-offload (LRO) and generic-receive-offload (GRO) for those interfaces. (Your network interface will not be the same as 'ens33')
```
sudo ethtool -k ens33 | grep receive-offload
```
![image02](https://github.com/user-attachments/assets/86a6fb6e-f713-4855-a511-179d8aba3b6b)

### Create the systemD script
```
sudo nano /lib/systemd/system/ethtool.service
```

Enter the following information, replace 'ens33' with your interface name
```
[Unit] Description=Ethtool Configration for Network Interface 

[Service] 
Requires=network.target 
Type=oneshot 
ExecStart=/sbin/ethtool -K ens33 gro off 
ExecStart=/sbin/ethtool -K ens33 lro off 

[Install] WantedBy=multi-user.target
```
![image03](https://github.com/user-attachments/assets/2fdd7ef7-4bb1-460b-8341-60ccf82ba69f)

Once the file is created, enable and start the service
```
sudo systemctl enable ethtool 
sudo service ethtool start
```
![image04](https://github.com/user-attachments/assets/2e04a0fd-5693-4281-93fe-e3d480aa89c8)

These settings will now persist across reboots. You can verify the setting using ethtool as above.
```
sudo ethtool -k ens33 | grep receive-offload
```

Enable promiscuous mode
```
sudo ip link set dev ens33 promisc on
```
<h2 align="left"> Configuring Snort </h2>

Create some folders and files that Snort requires for rules
```
sudo mkdir /usr/local/etc/rules
sudo mkdir /usr/local/etc/so_rules/
sudo mkdir /usr/local/etc/lists/

sudo touch /usr/local/etc/rules/local.rules
sudo touch /usr/local/etc/lists/default.blocklist

sudo mkdir /var/log/snort
```

You can write your own snort rules by yourself or (by using a snort rule creation website called "SNORPY") under local.rules file where we created eariler
![image05](https://github.com/user-attachments/assets/4c905fff-fbc7-44f0-8833-7fe5e9bbde13)

My created local test-rules
![image06](https://github.com/user-attachments/assets/57e39afa-e0f0-4c2a-9a4a-a9db3e1d5f38)


Adding local rules to Snort default configuration files
```
nano /usr/local/snort/etc/snort/snort.lua
```

Hit Ctrl+W and search for ips

![image07](https://github.com/user-attachments/assets/7f1fc84b-6e96-44ae-9713-093e1aedeb65)

Uncomment 'enable_builtin_rules = true,' and add your path where your local.rules are located.
(In this case, I added 'include = "/usr/local/etc/rules/local.rules",')

![image08](https://github.com/user-attachments/assets/bd53a532-9ac5-4920-9d0b-a8a981751f1e)


Test our local rules with snort configuration file
```
sudo snort -c /usr/local/snort/etc/snort/snort.lua -R /usr/local/etc/rules/local.rules
```
![image09](https://github.com/user-attachments/assets/9585ce3c-14ec-4b31-82a1-513a4048f0b3)

![image10](https://github.com/user-attachments/assets/2c3a08e4-7ef9-4871-b2a5-f091bc15732d)

Now let's start listening traffic on the interface 'ens33' which I enabled promiscuous mode earlier.
```
sudo snort -c /usr/local/snort/etc/snort/snort.lua -R /usr/local/etc/rules/local.rules -i ens33 -A alert_fast
```
![image11](https://github.com/user-attachments/assets/de17b066-57b8-4036-87a4-e3994f8ebf5f)

Generate ICMP traffic to Snort listening interface 'ens33'

![image12](https://github.com/user-attachments/assets/919363cc-8a03-4bfa-9473-7cf92e802996)

**We can now see "ICMP Traffic Detected" logs from our Snort local.rules**

![image13](https://github.com/user-attachments/assets/52813f77-0c68-449f-a44d-91bc3094182e)


