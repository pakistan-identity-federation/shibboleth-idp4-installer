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


6. Install Ansible Package
   ```bash
     sudo apt install ansible
   ```


7. For new installations download the bootstrap-v4.ini file as follows;
   ```bash
      curl https://raw.githubusercontent.com/pakistan-identity-federation/shibboleth-idp4-installer/master/bootstrap-v4.ini > bootstrap-v4.ini
   ```
   Edit the bootstrap-v4.ini file; 
   
   A description of each field is provided [here](https://support.aaf.edu.au/en/support/solutions/articles/19000119843) and in the downloaded version of bootstrap-v4.in

   `vi bootstrap-v4.ini`

     *    You MUST review, configure and uncomment each field listed in the [main] section

     *    If you have LDAP details you SHOULD also configure the [ldap] section


8.   Running the installer 
     
     Download and prepare the bootstrap-v4.sh for execution using the following command;
     ```bash
        curl https://raw.githubusercontent.com/pakistan-identity-federation/shibboleth-idp4-installer/master/bootstrap-v4.sh > bootstrap-v4.sh && chmod u+x bootstrap-v4.sh
   ```
     
     


10. 
## Environmental data for your IdP

7. The following information is required by the IdP Installer and must be populated into the bootstrap-v4.ini file prior to running the installer.

`Item	                                    Purpose
Host Name	The public domain name of the IdP. May be used in determined the entityID of the IdP.
Entity ID	The unique technical name of the IdP. If migrating from an older IdP then its entity id should be used on the new IdP.
Environment	

A determination of the PKIFED federation you wish to register your IdP within, being test or production. AAF Support can assist you in determining this
Organisation Name	The human readable display name of your organisation
Organisation base domain	e.g. example.edu, used for the scope of user's scoped attributes
Install base	Where in the file system you want the IdP to be installed. The default is /opt
Patch System Software	If enabled, the operating system software will be updated every time the IdP is deployed, that is the command "yum update -y" will be executed. If you have your own system patching regime in place you can disable this feature.
(Default is enabled.)
eduGAIN enabled	Additional configuration is enabled to allow the IdP to technically connect to eduGAIN. See AAF eduGAIN for more information and how to join eduGAIN. (Default is NOT enabled.)
Local firewall enabled	If enabled, firewalld will be enabled and configured on the server allowing. (Default is enabled.)
Back-Channel enabled	If enabled, the IdP will support back-channel requests. (Default is NOT enabled.)`

