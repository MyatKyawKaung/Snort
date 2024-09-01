### PulledPork is a tool that is used to download snort or suricata rulesets, which are the latest rules files that snort/talos releases to ensure that your system can detect the latest attacks.

Start by obtaining the latest version of PulledPork3

```
cd ~/snort_src/ 
git clone https://github.com/shirkdog/pulledpork3.git
```
![image01](https://github.com/user-attachments/assets/3ec7bad6-c9f5-4caa-b43b-3e60c45e82e8)

```
cd ~/snort_src/pulledpork3 
sudo mkdir /usr/local/bin/pulledpork3 
sudo cp pulledpork.py /usr/local/bin/pulledpork3 
sudo cp -r lib/ /usr/local/bin/pulledpork3

sudo chmod +x /usr/local/bin/pulledpork3/pulledpork.py 

sudo mkdir /usr/local/etc/pulledpork3 
sudo cp etc/pulledpork.conf /usr/local/etc/pulledpork3/
```
**Verify Pulledpork3**
```
/usr/local/bin/pulledpork3/pulledpork.py -V
```
![image02](https://github.com/user-attachments/assets/f6348913-40c7-4ee3-a8bf-cb2c6f9f645d)

**Modify our pulledpork.conf file**
```
sudo nano /usr/local/etc/pulledpork3/pulledpork.conf
```

**Register with snort.org for a free oinkcode**. </br>
This unique code will allow you to download the “registered” and “LightSPD” rulesets . Without an Oinkcode, you are limited to downloading the “community” rulesets, which do not have many recent rules.

![image03](https://github.com/user-attachments/assets/4306deba-a7e1-42d9-a773-23a6ec22cc08)


Change registered_ruleset to true and set your oinkcode you get from your snort3 account

![image04](https://github.com/user-attachments/assets/5d0127ea-6936-4aa9-8d97-69d1d2cfa5e8)

Make sure the snort exe path, local rules path are correct and uncomment that out

![image05](https://github.com/user-attachments/assets/5c0152cf-f25b-4bcd-9a97-04390ff0181a)
![image06](https://github.com/user-attachments/assets/d8a8d39b-8247-4cb8-977c-9965e9d53c0b)

**Test Pulledpork3.py with its configuration file**

```
sudo /usr/local/bin/pulledpork3/pulledpork.py -c /usr/local/etc/pulledpork3/pulledpork.conf
```

**If this warning error pops up, we need to check the pulledpork.py to point the latest rule package to download**

![image07](https://github.com/user-attachments/assets/0e0f6909-d492-4cd8-af84-c64d242e424b)
![image08](https://github.com/user-attachments/assets/9f8f46ca-9acb-4cf2-9bb1-300f229366a2)
![image09](https://github.com/user-attachments/assets/38c75142-09f0-4eed-9381-701fce8cbeb9)


**Modify the python file <VERSION> into your latest package version number**

![image10](https://github.com/user-attachments/assets/b1854813-ab8b-43ef-8a2e-7381bffdaddb)


Now we rerun this command to test pulledpork.py

```
sudo /usr/local/bin/pulledpork3/pulledpork.py -c /usr/local/etc/pulledpork3/pulledpork.conf
```
![image11](https://github.com/user-attachments/assets/50e67d23-19b0-4cd9-89e9-3e323ba63987)


**We need to modify our snort config to point Pulledpork rules**

```
nano /usr/local/snort/etc/snort/snort.lua
```
![image012](https://github.com/user-attachments/assets/bc196c29-9d10-48a1-a748-ec33e2e1367b)

**You can check pulledpork rules you've installed by running**
```
head -20 /usr/local/etc/rules/pulledpork.rules
```
![image13](https://github.com/user-attachments/assets/bdccb4f2-1705-415b-bc82-3fd9557a0559)

Run snort to test the config

```
snort -c /usr/local/snort/etc/snort/snort.lua --plugin-path /usr/local/etc/so_rules/
```
![image14](https://github.com/user-attachments/assets/355da89f-afca-42d0-b9c4-2160304cfb6a)

