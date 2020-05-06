---
layout: post
comments: true
toc: true
toc_title: Contents
title:  "BOINC from the CLI"
subtitle:  "Install and setup BOINC on a headless Ubuntu Server"
image: /pics/BOINC_logo.png
date:   2020-05-05
categories: linux ubuntu headless boinc  
---

![My Boinc Stats](https://www.boincstats.com/signature/-1/user/90689675018/sig.png)

## Save the world from the safety of your command line

Whether you want to fight COVID-19, search for extraterrestrial lifeforms or research elementary particles, you can help by installing BOINC.

BOINC is a platform that runs scientific calculations on your pc, phone, laptop or Raspberry Pi. You can help your favorite organizations to computer power they would never be able to buy.

Today, almost a million computers are actively connected to the "grid". Together they've already achieved great things on the subjects of Cancer, HIV, Malaria, Alzheimer's, astrophysics, elementary particles and much more.

**This is is what the internet is made for!**

## Unobtrusive 

BOINC is a very small, lightweight program that only runs at full power when your computer is idle. You can always change the settings if you want the app to be more or less active.

## Install BOINC on headless Ubuntu Server

For Manjaro/Arch, check the [Arch Wiki](https://wiki.archlinux.org/index.php/BOINC)  

**Install BOINC using aptitude:**
```bash
sudo apt -y install boinc-client
```

### Setup BOINC from the command line

In this example I'm going to add the Rosetta@Home project. But of course you can add any project.  

**Start the service**  
(this will create the empty config files)
```bash
sudo systemctl start boinc-client.service
```

**Create a NEW account**  
(skip if you already have an account)
```bash
# boinccmd --create_account URL EMAIL PASSWORD NICKNAME
boinccmd --create_account https://boinc.bakerlab.org/rosetta user@example.com p@55w0rd Nickname
```
You should see something like this:
```bash
status: Success
poll status: operation in progress
account key: 2152889_3e1767baafcdb03ee986240a4431e11e
```
* Copy your account key for the next step. (You can repeat this step if you've lost your key)

**Add your project:**
```bash
# boinccmd --project_attach URL KEY
boinccmd --project_attach https://boinc.bakerlab.org/rosetta 2152889_3e1767baafcdb03ee986240a4431e11e
```
* The key in this example is my weak key. Feel free to use it.  

**Check if everything is working fine**
```bash
# It might take a few minutes for the application to download the user details
watch boinccmd --get_simple_gui_info
```

**Start BOINC automatically on boot**
```bash
sudo systemctl enable boinc-client.service
```

### Restrict CPU usage

If you use your computer for other applications or if your system can't handle a constant 100% load, you might want to limit the CPU usage

You can override the global settings by editing: **/var/lib/boinc-client/global_prefs_override.xml**

```bash
sudo nano /var/lib/boinc-client/global_prefs_override.xml
```

Add the following line to limit the CPU load to max **40%**:

```bash
<global_preferences>
    <cpu_usage_limit>40</cpu_usage_limit>
</global_preferences>
```

After saving the new settings, issue the following command:

```bash
 boinccmd --read_global_prefs_override
 ```