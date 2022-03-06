# Shibboleth IdP Version 4 Installer

## Overview
This Shibboleth IdP Installer is designed to automate the install of version 4 for the [Shibboleth IdP](https://shibboleth.atlassian.net/wiki/spaces/IDP4/overview) on a dedicated with one of the following supported operating systems;
* Ubuntu 20.04 (Focal Fossa)
* Rocky Linux 8
* CentOS 7, 8 or Stream
* RedHAT 7 or 8
* ORACLE Linux 7 or 8

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
     
     Ensure that bootstrap-v4.ini is in the same directory used to download the bootstrap-v4.sh script.
     
     Run the following command as root;
     `./bootstrap-v4.sh`
     
     The bootstrap-v4 process will now install and configure your server to operate as a Shibboleth IdP. 
     

9. Event logging

   The installer provides a detailed set of information indicating the steps it has undertaken on your server. You MAY disregard this output if the process completes            successfully.

   For future review all installer output is logged to
   
   `/opt/shibboleth-idp-installer/activity.log`

10. Check the IdP logs
    
    The IdP writes its four log files into the directory `/var/log/shibboleth-idp`. It may take a few minutes for the log files to appear the first time the IdP starts.

    Check `idp-warn.log` for any errors. This should only contain two Deprecation warnings that can be ignored.

    Check `idp-process.log` from any ERRORS and and verify the Servlet has started. You should find the following content near the end of the file.
    
    
   
   ## Errors during installation

   If an error occurs, the logs prior to installer termination MUST be reviewed to understand the underpinning cause.

   Generally the installer SHOULD be executed once.

   After the initial execution you’ll receive an error if you try to run bootstrap.sh again.

   You MUST NOT re-run bootstrap-v4.sh if the installation process completed but you made a simple mistake. e.g.

    *   Mistyped config in the [main], [ldap] or [advanced] sections
    
   If you force bootstrap-v4.sh to run again once initial installation has completed the action MAY be destructive.
    
   ## Allowing the installer to run again
 
   In general you will never need to re-run the bootstrap-v4.sh script after it has completed creating the `/opt/shibboleth-v4-installer` directory. 
    
   You should use the `deploy` or `upgrade` scripts instead.
    
   If you must re-run bootstrap-v4.sh then you remove the lock file first. Note this will overwrite any previous installations.
    
`    rm /root/.lock-idp-bootstrap-v4 && ./bootstrap-v4.sh`

   The bootstrap-v4 process will now start over and attempt to install and configure your server to operate as a Shibboleth IdP.


   ## When Bootstrap-v4.sh completes

  When the scripts completes the mariadb, jetty and the IdP should be running. The IdP probably will not be able to authenticate and there may be some errors in the             Shibboleth log file /var/log/shibboleth-idp/idp-warn.log. This is a good start but indicates that additional configuration is required.


## Environmental data for your IdP in bootstrap-v4.ini 

The following information is required by the IdP Installer and must be populated into the bootstrap-v4.ini file prior to running the installer.

**Host Name**	The public domain name of the IdP. May be used in determined the entityID of the IdP.

**Entity ID**	The unique technical name of the IdP. If migrating from an older IdP then its entity id should be used on the new IdP.

**Environment**	 A determination of the PKIFED federation you wish to register your IdP within, being test or production. 

**Organisation Name**	The human readable display name of your organisation

**Organisation base domain**	e.g. example.edu, used for the scope of user's scoped attributes

**Install base**	Where in the file system you want the IdP to be installed. The default is /opt 

**Patch System Software**	If enabled, the operating system software will be updated every time the IdP is deployed, that is the command "yum update -y" will be executed. If you have your own system patching regime in place you can disable this feature. (Default is enabled.)

**eduGAIN enabled**	Additional configuration is enabled to allow the IdP to technically connect to eduGAIN. See AAF eduGAIN for more information and how to join eduGAIN. (Default is NOT enabled.)

**Local firewall enabled**	If enabled, firewalld will be enabled and configured on the server allowing. (Default is enabled.)

**Back-Channel enabled**	If enabled, the IdP will support back-channel requests. (Default is NOT enabled.)

