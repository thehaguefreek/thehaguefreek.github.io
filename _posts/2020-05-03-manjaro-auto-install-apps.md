---
layout: post
comments: true
toc: true
toc_title: Contents
title:  "Ubuntu app install script"
subtitle:  "How to create a simple script to automatically install all your favourite applications"
image: /pics/Ubuntu-1404-VPS.jpg
date:   2020-05-03
categories: ubuntu script install apps applications
---

## Why Linux is just better

**Jump directly to the final [example script](#full-script).**

In his popular video ["10 ways Linux is just better!" ](https://www.youtube.com/watch?v=4halg2kzPms), Linus Tech Tips talks about a developer with a script that installs all the apps he needs in no time.  
Let's create that script!

Linux was the first OS introducing an "app store". Since you can install almost any app from this app store (or package manager), it's dead simple to create a small script that installs all your favourite programs.

Since every Linux distro has it's own package manager, the installation process is slightly different for every distribution. Today we'll create a script for Ubuntu.

## The two Package managers of Ubuntu

Ubuntu uses aptitude as main packange manager. A couple of years ago Ubuntu introduces snap packages. Snap apps run in a container and don't rely on system wide dependencies. I'll include both aptitude and snap in this post

## Part 1: Aptitude Apps

Install aptitude applications. Add -y to install automatically without asking questions.
``` bash
sudo apt -y install app_name
``` 

### Aptitude example

Now we just have to put our favourite apps in an array and loop over this array to install aptitude apps:

```bash
# Array with applications
aptApps=(
    # Editors
    nano
    gedit

    # Benchmarking
    htop
    stress
)

# Install Aptitude applications
for i in "${aptApps[@]}"
do
    sudo apt -y install "$i"
done
```

## Part 2: Snap Store

Snap is very similar the aptitude process.


The command to install snap apps:
```bash
sudo snap install app_name
```
**\*** Some apps need permission to run like a classic app. Add **--classic** in this case


## Full script

Let's combine everything in a single script to install all your favourite Linux/Manjaro/Arch apps. 

```bash
# Snap Apps
aptApps=(
    # Editors
    nano
    gedit

    # Benchmarking
    htop
    stress
)

# Snap Apps
declare -A snapApps
snapApps=(
    # Editors
    --classic code   
)

# Install Pacman applications
for i in "${pacmanApps[@]}"
do
        sudo pacman -S "$i" --needed --noconfirm    # Install app if needed, without asking questions
done

# Install Pamac applications
for i in "${!pamacApps[@]}"
do
    if hash "$i" 2>/dev/null;    # Check if command (app) is available
    then
        echo "$i already installed"
    else
        pamac build "${pamacApps[$i]}" # Install app
    fi
done

# Install Snap applications
for i in "${!snapApps[@]}"
do
    if hash "$i" 2>/dev/null;    # Check if command (app) is available
    then
        echo "$i already installed"
    else
        sudo snap install "${snapApps[$i]}" # Install app
    fi
done
```


