# Shibboleth IdP Version 4 Installer

## Overview
The AAF Shibboleth IdP Installer is designed to automate the install of version 4 for the [Shibboleth IdP](https://shibboleth.atlassian.net/wiki/spaces/IDP4/overview) on a dedicated with one of the following supported operating systems;
* Rocky Linux 8
* CentOS 7, 8 or Stream
* RedHAT 7 or 8
* ORACLE Linux 7 or 8
* Ubuntu 20.04 (Focal Fossa)

## Status
This installer is forked from AAF's shibboleth idp installer repository with some modifications to work with Pakistan Identity Federation (PKIFED). This software is actively being developed and maintained. It is ready for use in production environments.

This version now enables the technical connection to eduGAIN, the global federation of federations.

This release is for Shibboleth version 4.1.5 running on Jetty 9.4.44

## License
Apache License Version 2.0, January 2004

## Required Checklist
A dedicated Ubuntu 20.04 (virtual or physical) or RedHAT 7 or 8 or CentOS 7, 8 or Stream, with the following minimum specifications: 

    2 CPU
    4GB RAM
    10GB+ partition for OS

## Server connectivity

* You MUST have SSH access to the server
* You MUST be able to execute commands as root on the system without limitation
* The server MUST be routable from the public internet with a static IP. Often this means configuring the IP on a local network interface directly but advanced environments may handle this differently.
* The static IP MUST have a publicly resolvable DNS entry. Typically of the form idp.example.edu
* The server MUST be able to communicate with the wider internet without blockage due to firewall rules.


## Installation Steps

1. Install Ubuntu 20.04.4 LTS or your selected OS (list given above).

2. Become ROOT:
   * `sudo su -`

3. Update the OS 
   ```
   apt-get update && apt-get upgrade
   ```
  
4. Change TimeZone w.r.t to your country:
   ``` 
   sudo timedatectl set-timezone Asia/Karachi 
   ```
   
5. Set the IdP hostname, The public domain name of the IdP may be used in determined the entityID of the IdP.

    (**ATTENTION**: *Replace `idp.example.org` with your IdP Full Qualified Domain Name and `<HOSTNAME>` with the IdP hostname*)

   * `vim /etc/hosts`

     ```bash
     <YOUR SERVER IP ADDRESS> idp.example.org <HOSTNAME>
     ```

   * `hostnamectl set-hostname <HOSTNAME>`
