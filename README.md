# Introduction

## About DigitalOcean

DigitalOcean is a cloud computing vendor and service provider. DigitalOcean offers multiple services, but the primary service we will be making use of in this setup guide is DigitalOcean’s Droplet. A **Droplet** is a Linux-based virtual machine that is hosted on DigitalOcean that will be a server for us to remotely access.

## Requirements

This guide is designed for users who are familiar with idea of remote cloud-based servers, what Linux is, but are not sure on how to set up a cloud-based server to host and manage. For this guide, we will be specifically using the **Arch Linux image** and **Windows operating systems**. Settings and commands will vary based on the user’s operating system. 

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
4. Installing `doctl`
5. Configuring API Access for `doctl`
6. Initializing Droplet setup with `doctl` and Cloud-init
7. Connecting to your Droplet with SSH
8. Deploying another Droplet with `doctl`

# Working with SSH key pairs

An **SSH key** is an authentication method used to gain access to encrypted connections. Why *use an SSH key over just a password?* Compared to an SSH Key, passwords can be considered less secure and ineffective as they can be easily cracked and can be weak.

The key pair is based on two related but asymmetric keys: a private key and a public key both creating the key pair used as a secure access credential. For the purpose of this tutorial, we will be using the SSH key pair to connect to the remote server.

## Creating the SSH Key

1. Open the **Terminal** in your Start Menu
2. Type and run the command `cd ~` to change to your home directory
3. Type and run the command `mkdir .ssh` to make our `.ssh` directory.
	This folder is where we are storing our SSH keys
4. Type and run the command `ls` to list the contents of the directory to confirm we have made the `.ssh` directory.
   ![[ssh-directory.png]]

5. Type and run the command below to generate your keys.
   
```
ssh-keygen -t ed25519 -f C:\Users\<your-user-name>\.ssh\<key-name> -C "youremail@email.com"
```

- `ssh-keygen` is the authentication key generation command
- `-t ed25519` specifies the type of key and we are using the protocol `ed25519`
- `-f C:\Users\<your-user-name>\.ssh\<key-name>` specifies the file name at our designated directory and desired key-name
- `-C "youremail@email.com"` adds a comment with our email

**Note**: Replace the text within the “<>” tags with your respective information. For example, you will replace “your-user-name” with your user found in the directory path name. You will also replace “youremail@email.com" with your desired email.

![[ssh-keygen-example.png]]

6. Type a passphrase. You will need to remember this passphrase to securely access the server we will be making.

After choosing your passphrase, at this point your SSH key pair has been created. Next we will learn to link it to our DigitalOcean account.

# Adding an SSH Public key to your DigitalOcean Account

We have successfully created the SSH key pair. We will have to add the public key to our DigitalOcean account to give us a secure way of logging into our server.

1. Type and run the command below into the **Terminal** to copy the public key
   
```
Get-Content C:\Users\<your-user-name>\.ssh\<your-key-name>.pub | Set-Clipboard
```

- `Get-Content C:\Users\<your-user-name>\.ssh\<your-key-name>.pub` grabs the content of the file at our specified directory and desired file. For our example, we are grabbing the text contents of our public key file
- `|` is our pipeline to connect the output of our `Get-Content` and input it to our next command
- `Set-Clipboard` will copy our output from the pipeline, in this case our public key, to the clipboard to allow us to paste our text

**Note**: Like the previous step, make sure you replace the text within the “<>” tags with the respective details. Going forward for this tutorial, you will be expected to remember to do this!

2. Log in to your **DigitalOcean Account**
3. Click **Settings** on the left navigation bar
   ![[settings-menu.png]]
4. Click the **Security** tab > **Add SSH Key** button
   ![[add-ssh-key.png]]
5. Paste the **Copied Key** into the **Public Key** box and decide on a key name
   
   ![[paste-public-key.png]]
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

# Installing and Configuring doctl

`doctl` is DigitalOcean’s official command-line interface (CLI). It allows you to interact with the DigitalOcean API via the command line. For us this means it will let us create, configure, and destroy DigitalOcean resources, specifically Droplets.

**Warning**: When running the following commands in the Terminal, ensure that it is **Run as Administrator**

1. Open the **Terminal**
2. Type and run **the command below** to download the most recent version of `doctl`:

```
Invoke-WebRequest https://github.com/digitalocean/doctl/releases/download/v1.110.0/doctl-1.110.0-windows-amd64.zip -OutFile ~\doctl-1.110.0-windows-amd64.zip
```

- `Invoke-WebRequest` sends a request to a webpage to grab its contents. In this case we are grabbing the .zip file for `doctl` and outputting the zip file only.

3. Type and run **the command below** to extract the `doctl` binary:

```
Expand-Archive -Path ~\doctl-1.110.0-windows-amd64.zip
```

- `Expand-Archive` extracts the zipped files to our specified `-Path` and our desired file, in this case it is our .zip file.

4. Finally, type and run **the command below** to move the `doctl` binary into a new dedicated directory, and add it to your system’s path:

```
New-Item -ItemType Directory $env:ProgramFiles\doctl\
Move-Item -Path ~\doctl-1.110.0-windows-amd64\doctl.exe -Destination $env:ProgramFiles\doctl\
[Environment]::SetEnvironmentVariable(
    "Path",
    [Environment]::GetEnvironmentVariable("Path",
    [EnvironmentVariableTarget]::Machine) + ";$env:ProgramFiles\doctl\",
    [EnvironmentVariableTarget]::Machine)
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
```

Alternatively, you can run the commands separately.
```
New-Item -ItemType Directory $env:ProgramFiles\doctl\
```

- `New-Item -ItemType Directory` is the command to create a specified file with a specified file type, and in this case a directory. It is created at a specified path and desired name at the end of the path.

```
Move-Item -Path ~\doctl-1.110.0-windows-amd64\doctl.exe -Destination $env:ProgramFiles\doctl\
```

- `Move-Item` command moves an item and its properties and contents from the specified path and file name to our desired `-Destination` at the next specified path

```
[Environment]::SetEnvironmentVariable(
    "Path",
    [Environment]::GetEnvironmentVariable("Path",
    [EnvironmentVariableTarget]::Machine) + ";$env:ProgramFiles\doctl\",
    [EnvironmentVariableTarget]::Machine)
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
```

- In Windows, Environment Variables store information related to the current environment and affects the way running processes and files will behave on the device. In our case, this command will add the `doctl` path to our environment, which will allow us to run it from any terminal session.

After the installation, we cannot quite use `doctl` yet. We will have to authenticate the DigitalOcean API to allow it access to `doctl`.
# Configuring API Access for doctl

To manage our droplet, we would need to use the DigitalOcean API, but we will need to configure who we want to use the API and access our servers. Users will use the Personal Access Token to authenticate themselves and request access to the API to use `doctl`.
# 
# Notes
- Need upload custom image notes