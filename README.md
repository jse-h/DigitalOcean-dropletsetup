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
2. Installing `doctl`
3. Configuring API Access for `doctl`
4. Uploading the Public Key to DigitalOcean account with `doctl`
5. Uploading a Custom Image to DigitalOcean with `doctl`
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

After choosing your passphrase, at this point your SSH key pair has been created. We will need to use `doctl` to upload our public key to our DigitalOcean account. Next we will install and configure `doctl`.
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

## Generating API Token

1. Click **API** on the left navigation bar
2. Click **Generate New Token**
3. Type `<your-Token-Name>` and choose the **Full Access** scopes.
4. Click **Generate Token**
5. Copy and Paste the **personal access token**

**Caution**: Token is shown only once, copy and paste this token somewhere safe.

## Granting account access to `doctl`

1. Open the **Terminal**
2. Type and run **the command below** to grant `doctl` access:
   
```
doctl auth init --context <NAME>
```

This command allows you to initialize `doctl` with a token so you will be able to query and manage account details and resources. Give the authentication context a name and replace `<NAME>`.

3. Paste in your **personal access token** that you have stored
![[token-auth.png]]

4. Validate that `doctl` is working by running:
```
doctl account get
```

- This command is used to retrieve the details from your account profile.

If successful your output should look like:
```
User Account          Team     Droplet Limit   Email Verified  UUID             Status
youremail@email.com  My Team         10           true         <uuid-numbers>   Active 
```

![[doctl-account-get.png]]

Now that we have successfully configured `doctl`, we can start using it, first by adding the SSH public key we created to our DigitalOcean account.
# Uploading the Public Key to DigitalOcean account with `doctl`

We will be using `doctl` to grab and upload our public key to our DigitalOcean account.

1. Open the **Terminal**
2. Type and run **the command below**:
   
```
doctl compute ssh-key import "My SSH Key" --public-key-file ~/.ssh/<key-name>.pub
```

- `doctl compute ssh-key import "My SSH Key"` is the subcommand of `doctl` that imports an existing SSH key from our local machine to our account. We would replace “My SSH Key” with the key name we want it to be named on the DigitalOcean account.
- `--public-key-file` specifies that we want a public key file
- `~/.ssh/<key-name>.pub` specifies the path we point to our public key file, if you are already in the home directory, you can try just a relative path of `.ssh/<key-name>.pub`

If successful, you should see an output that looks like:

```
ID           Name              FingerPrint
43529188     droplet-guide     b9:ce:b0:01:51:fe:2b:23:60:18:e6:7e:1a:5c:4c:31
```

Next we have to upload our custom image of Arch Linux onto DigitalOcean to use for our remote server.
# Uploading a Custom Image to DigitalOcean

Custom images are Linux distributions. The custom image we will be working with in this tutorial is Arch Linux, a lightweight and flexible Linux distribution. We will learn to upload this custom image to our DigitalOcean account to create our server with our preferred image.

# Notes
- Need upload custom image images