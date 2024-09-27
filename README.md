# Introduction

## About DigitalOcean

DigitalOcean is a cloud computing vendor and service provider. DigitalOcean offers multiple services, but the primary service we will be making use of in this setup guide is DigitalOcean’s Droplet. A **Droplet** is a Linux-based virtual machine that is hosted on DigitalOcean that will be a server for us to remotely access.

## Requirements

This guide is designed for users who are familiar with idea of remote cloud-based servers, what Linux is, but are not sure on how to set up a cloud-based server to host and manage. For this guide, we will be specifically using the Arch Linux image and for Windows operating systems. Settings and commands will vary based on the user’s operating system. 

Users should also be familiar with or have:

1. what Linux is,
2. some knowledge of command-line tools
3. a DigitalOcean account
4. an Arch Linux image
## Instructions Overview

These instructions will walk you through several steps required for setting up the droplet using SSH keys and the `doctl`.

1. Working with SSH key pairs
2. Connecting a SSH Public Key to your DigitalOcean Account
3. Uploading a Custom Image to DigitalOcean
4. Installing and Configuring `doctl`
5. Initializing Droplet setup with `doctl` and Cloud-init
6. Connecting to your Droplet with SSH
7. Deploying another Droplet with `doctl`

# Working with SSH key pairs

An **SSH key** is an authentication method used to gain access to encrypted connections. The key pair is based on two related but asymmetric keys: a private key and a public key both creating the key pair used as a secure access credential. For the purpose of this tutorial, we will be using the SSH key pair to connect to the remote server.

## Creating the SSH Key

1. Open the **Terminal** in your Start Menu
2. Type the command `cd ~` to change to your user directory
3. Type the command `mkdir .ssh` to make our `.ssh` folder.
	This folder is where we are storing our SSH keys
4. Type the command `ls` to list the contents of the file to confirm we have made the `.ssh` directory.
   ![[ssh-directory.png]]

5. Type the command below to generate your keys.
```
ssh-keygen -t ed25519 -f C:\Users\<your-user-name>\.ssh\<key-name> -C "youremail@email.com"
```

**Note**: Replace the text within the “<>” tags with your respective information. For example, you will replace “your-user-name” with your user found in the directory path name. You will also replace “youremail@email.com" with your desired email.

![[ssh-keygen-example.png]]

6. Type a passphrase. You will need to remember this passphrase to securely access the server we will be making.

After choosing your passphrase, at this point your SSH key pair has been created. Next we will learn to link it to our DigitalOcean account.

# Adding an SSH Public key to your DigitalOcean Account

We have successfully created the SSH key pair. We will have to add the public key to our DigitalOcean account to give us a secure way of logging into our server.

1. Type the command below into the **Terminal** to copy the public key
   
```
Get-Content C:\Users\<your-user-name>\.ssh\<your-key-name>.pub | Set-Clipboard
```

**Note**: Like the previous step, make sure you replace the text within the “<>” tags with the respective details. Going forward for this tutorial, you will be expected to remember to do this!

2. Log in to your **DigitalOcean Account**
3. Click **Settings** on the left navigation bar
   ![Settings Menu](/attachments/settings-menu.png)
4. Click the **Security** tab > **Add SSH Key** button
   ![Adding SSH Key](/attachments/add-ssh-key.png)
5. Paste the **Copied Key** into the **Public Key** box and decide on a key name
   ![Paste Public Key](/attachments/paste-public-key.png)
6. Click **Add SSH Key**

With our SSH Key, we can now upload our Arch Linux image for the first steps into creating the server.

# Uploading a Custom Image to DigitalOcean

Custom images are Linux distributions. The custom image we will be working with in this tutorial is Arch Linux, a lightweight and flexible Linux distribution. We will learn to upload this custom image to our DigitalOcean account to create our server with our preferred image.

1. While in a DigitalOcean Project, Click **Manage** on the left navigation bar to expand more options.
2. Click **Backups & Snapshots** > **Custom Images** tab
3. Click **Upload Image**
4. Navigate and choose the **Arch Linux image** you have downloaded
5. Click **Distribution** and choose **Arch Linux** to match our custom image
6. Click your **closest region** 
7. Click **Upload Image**