## Snort-Telegram-Bot-Application (Intrusion Detection System)

Programs are created using a bash script that is linked to Telegram's API to provide a notification message that an attack has occurred.

### System Network Topology

![Topologi Jaringan Sistem](https://user-images.githubusercontent.com/52452132/173330308-c7f677a8-f4b8-4dcf-a839-16605229bd8b.png)

### System Flow

![Alur Sistem](https://user-images.githubusercontent.com/52452132/173330529-05eba462-a415-4e04-af09-2ae15b8cca17.png)

### Installation

Clone repository

```bash
https://github.com/IrvanDimetrio/Snort-Telegram-Bot-Application.git
```

Snort Installation

```bash
sudo apt-get install snort -y
```

### Telegram Bot

Creating Telegram bot API notification messages requires registration in the @BotFather account; @BotFather is the official Telegram account used to create Telegram bots; first, in the search bar of the Telegram app, type @BotFather; then, click on Start to start the Telegram bot registration process; type /next start/newbot

@BotFather will issue a telegram api bot token once the bot registration process is complete. This token will be used to get Telegram ID on mobile devices.

For the next step, which is to get the chat_id bot that has been created so that the message sent matches the bot that was created, use a web browser, the author uses chrome to access:
```
  https://api.telegram.org/bot[BotToken]/getUpdates
```

The last stage for making a telegram bot is by trying the chat_id obtained to send messages to the bot by accessing the url:
```
  https://api.telegram.org/bot[BotToken]/sendMessage?chat_id=[chat-id]&text=Coba%20Kirim%20Pesan
```

### Configuration

##### Edit snort configuration
```bash
  sudo gedit /etc/snort/snort.conf
```
Search for "ipvar HOME_NET".
Then change the "any" to your device IP Address.
Example : ipvar HOME_NET 192.168.56.101

##### Edit snort debian configuration
```bash
  sudo gedit /etc/snort/snort.debian.conf
```
Search for "DEBIAN_SNORT_HOME_NET".
And set your device IP Address.
Example : DEBIAN_SNORT_HOME_NET="192.168.56.101".

##### Edit snort local rules
```bash
  sudo gedit /etc/snort/rules/local.rules
```
Example local rules snort :
```bash
# LOIC DoS Attack
  alert udp $EXTERNAL_NET any -> $HOME_NET $HTTP_PORTS (msg:"SLR - LOIC DoS Tool (UDP Mode)  - Behavior Rule (tracking/threshold)"; threshold: type threshold, track by_src, count 100 , seconds 5; classtype:misc-activity;  sid:1000002; rev:1;)

# TCP Port Scanning
alert tcp any any -> $HOME_NET any (msg:”Nmap TCP Port Scanning”; detection_filter:track by_src, count 30, seconds 60; sid:1000003; rev:1;)

# Potential SSH Brute Force
alert tcp any any -> $HOME_NET 22 (msg: "Potential SSH Brute Force Attack Port 22"; sid: 1000004; rev:1;)

# Remotel Telnet Access
alert tcp any any -> $HOME_NET 23 (msg: "Potential Remote Telnet Access Port 23"; sid: 1000005; rev:1;)
```

##### Edit bot-telegram.sh file
```bash
  cd snort-telegram-bot-application/
  sudo gedit bot-telegram.sh
```
Change the username and then fill the chat_id and token variable and save the file.

Before we run the program, we will validate the configuration.
```bash
  sudo snort -T -c /etc/snort/snort.conf -i enp0s3
```
-i : Your network interface (Maybe yours is gonna be different)

You can check your network interface by running this command
```bash
  ifconfig
```
## Usage

First terminal
```bash
  sudo snort -i enps03 -c /etc/snort/snort.conf -l /var/log/snort -d -A console > /home/[username]/log.txt
```

Second terminal\
Go to telegram-bot-for-snort folder and run the program
```bash
  cd snort-telegram-bot-application/
  ./bot-telegram.sh
```

And then try to attack your device like ping or something else.
Example :
```bash
  ping 192.168.56.101 -t
```

### Screenshots

![ICMP Ping](https://user-images.githubusercontent.com/52452132/173334941-1a6a1f2b-5b7a-44dd-a350-e6b194df1921.png) ![ICMP Ping terminal](https://user-images.githubusercontent.com/52452132/173335118-43a5ae53-6eee-41f3-93ac-26bf8616fbd7.png) ![Bot Telegram Alert](https://user-images.githubusercontent.com/52452132/173335428-64fe0b68-6971-4228-855a-7af3e92a1221.jpg)

### Demo System

https://youtu.be/v6vRPD87CiQ
