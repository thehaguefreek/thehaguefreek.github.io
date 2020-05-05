---
layout: post
comments: true
toc: true
toc_title: Contents
title:  "Manjaro app install script"
subtitle:  "How to create a simple script to automatically install all your favourite applications"
image: /pics/Manjaro_Installation.png
date:   2020-05-03
categories: linux manjaro scripts install applications
---

## Why Linux is just better

**Jump directly to the final [example script](#full-script).**

In his popular video ["10 ways Linux is just better!" ](https://www.youtube.com/watch?v=4halg2kzPms), Linus Tech Tips talks about a developer with a script that installs all the apps he needs in no time.  
**Let's create that script!**

Linux was the first OS introducing an "app store". Since you can install almost any app from this app store (or package manager), it's dead simple to create a small script that installs all your favorite programs.

Since every Linux distro has it's own package manager, the installation process is slightly different for every distribution. Today we'll create a script for Manjaro.

## The three Package Managers of Manjaro

Manjaro uses Pacman as its main package manager. If you can't find your app in Pacman, you might find it in the Pamac manager. Pamac manages the packages from the Arch user repository. And if we can't find our app there we still have Snap Store as our last resort.

## Part 1: Pacman Apps

The install command for Pacman looks like this:
``` bash
sudo pacman -S app_name --needed --noconfirm
``` 
* **--needed** to prevent the reïnstallation of already installed apps.  
* **--noconfirm** to automatically install the application with no questions asked.

### Pacman example

Now we just have to put our favorite apps in an array and loop over this array to install Pacman apps:

```bash
# Array with applications
pacmanApps=(
    # Editors
    nano
    code

    # Gaming
    steam
)

# Install pacman applications
for i in "${pacmanApps[@]}"
do
    sudo pacman -S "$i" --needed --noconfirm
done
```

## Part 2: Pamac Apps

An automated script for pamac requires an extra step. Since Pamac doesn't check if an app is already installed, we have to do that first.

The command to check if an app is installed:
```bash
if hash app_name 2>/dev/null;    # Check if command (app) is available
```

The command to install a Pamac app:
```bash
pamac build app_name
```

We have to add one last bit. Since the command and package name often differ, we'll have to put both in our array.

### Pamac example

Format: [command]=package_name

```bash
declare -A pamacApps
pamacApps=(
    # Developement
    [symfony]=symfony-cli

    # Audio/Video
    [spotify]=spotify
)

# Install Pamac applications
for i in "${!pamacApps[@]}"
do
    if hash app_name 2>/dev/null;    # Check if command (app) is available
    then
        echo "$i already installed"
    else
        pamac build "${pamacApps[$i]}"
    fi
done

```

## Part 3: Snap Store

The command to install snap apps:
```bash
sudo snap install app_name
```

### Snap example
```bash
# Snap Apps
snapApps=(
    # Fun
    toilet-deej    
)

# Install Snap applications
for i in "${snapApps[@]}"
do
        sudo snap install "$i" # Install app
done
```

## Full script

Let's combine everything in a single script to install all your favorite Linux/Manjaro/Arch apps. 

```bash
# Pacman Apps
pacmanApps=(
    # Editors
    nano
    code

    # Gaming
    discord
    steam
)

Pamac Apps
declare -A pamacApps
pamacApps=(
    # Format: [command]=package_name

    # Developement
    [symfony]=symfony-cli

    # Audio/Video
    [spotify]=spotify
)

# Snap Apps
snapApps=(
    # Fun
    toilet-deej    
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
for i in "${snapApps[@]}"
do
        sudo snap install "$i" # Install app
done
```


