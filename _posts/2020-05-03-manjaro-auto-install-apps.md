---
layout: post
comments: true
title:  "Manjaro Auto Install Apps"
subtitle:  "How to create a simple script to automatically install all your favourite applications"
image: /pics/Manjaro_Installation.png
date:   2020-05-03
categories: manjaro script install apps applications
---

## Why Linux is just better

In his popular video ["10 ways Linux is just better!" ](https://www.youtube.com/watch?v=4halg2kzPms), Linus talks about a developer with a script that installs all the apps he needs to get up and running in no time.

Linux was the first OS introducing an "app store". Since you can install almost any app from this app store (or package manager), it's dead simple to create a simple script that installs all your favourite programs.

Since every Linux distro has it's own package manager the installation of application is slightly different. Today we'll create a script for Manjaro.

## What about settings?

I'll explain how to create a script that sets your favourite Manjaro settings in a future post.

## The three Package managers of Manjaro

Manjaro uses Pacman as its main package manager. If you can't find your app in Pacman, you might find it in the Pamac manager. Pamac manages the packages from the Arch user repository. And if we can't find our app there we still have Snap as our last resort.

## Part 1: Pacman

The install commnand for Pacman looks like this:
``` bash
sudo pacman -S app_name --needed --noconfirm
``` 
Add **--needed** to prevent the reïnstallation already installed scripts.
Add **--nocoffirm** to automatically install the app with no questions asked.

### Pacman example

Now we just have to create an array with apps and put the previous line in a for loop to start installing pacman apps:

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

## Part 2: Pamac

An automated script for pamac requires an extra step. Since Pamac doesn't check if an app is already installed, we have to do that first.

The command to check if an apps is installed:
```bash
if hash "$i" 2>/dev/null;    # Check if command (app) is available
```

The command to install Pamac apps:
```bash
pamac build app_name
```

We have to add one last bit. Since the command and package name often differ, we'll have to put both in our array.

### Pamac example

# Format: [command]=package_name

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
    if hash "$i" 2>/dev/null;    # Check if command (app) is available
    then
        echo "$i already installed"
    else
        pamac build "${pamacApps[$i]}"
    fi
done

```

## Part 3: Snap applications

Snap is very similar to the a Pamac process.

The command to check if an apps is installed (same as in Pamac):
```bash
if hash "$i" 2>/dev/null;    # Check if command (app) is available
```

The command to install snap apps:
```bash
sudo snap install "${snapApps[$i]}"
```

## Full script

Let's combine everything in a single script to install all your favourite Linux/Manjaro/Arch apps. 

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
declare -A snapApps
snapApps=(
    # Fun
    [toilet-deej.toilet]=toilet-deej    
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


