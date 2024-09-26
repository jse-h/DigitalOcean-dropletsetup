# Introduction

## About DigitalOcean

DigitalOcean is a cloud computing vendor and service provider. DigitalOcean offers multiple services, but the primary service we will be making use of in this setup guide is DigitalOcean’s Droplet. A **Droplet** is a Linux-based virtual machine that is hosted on DigitalOcean that will be a server for us to remotely access.

## Requirements

This guide is designed for users who are familiar with idea of remote cloud-based servers, what Linux is, but are not sure on how to set up a cloud-based server to host and manage. For this guide, we will be specifically using the Arch Linux image and for Windows operating systems. Settings and commands will vary based on Users should also be familiar with or have:

1. what Linux is,
2. some knowledge of command-line tools
3. a DigitalOcean account
4. an Arch Linux image (or another custom Image)
## Instructions Overview

These instructions are will walk you through several steps of setting up the remote server.

1. Working with SSH key pairs
2. Uploading a Custom Image to DigitalOcean
3. Connecting a SSH Public Key to your DigitalOcean account
4. Installing and Configuring `doctl`
5. Initializing Droplet setup with `doctl` and Cloud-init
6. Connecting to your Droplet with SSH
7. Deploying another Droplet with `doctl`

# Working with SSH key pairs

An **SSH key** is an authentication method used to gain access to encrypted connections. The key pair is based on two related but asymmetric keys: a private key and a public key both creating the key pair used as a secure access credential. For the purpose of this tutorial, we will be using the SSH key pair to connect to the remote server.

## Creating the SSH Key

1. Open the **Terminal** in your Start Menu