### Preface
This guide will allow you to replicate the network of VMs that I configured for this project. A working knowledge of Bash, PowerShell, and VirtualBox is assumed.

The deployment scripts assume a host system with specifications similar to the following:
* CPU: AMD Ryzen 7 5800x
* GPU: AMD RX 6600 XT
* RAM: 32GB DDR4
* STORAGE: 1TB (SSD strongly recommended) 

### Host Setup 
* Install Oracle VirtualBox 

* Run the script VM.ps1 if you are on a Windows host or VM.sh if you are on a Linux host, this will create and configure the seven virtual machines.

* Download the following OS images:
    * [Fedora Server 40](https://fedoraproject.org/server/download)
    * [Fedora Workstation 40](https://fedoraproject.org/workstation/download)
    * [Windows Server 2022](https://www.microsoft.com/en-us/windows-server)
    * [Windows 11](https://www.microsoft.com/software-download/windows11)

### Microsoft Network

#### MS_pfSense
* Select the first interface as the WAN interface

* Select the second interface as the LAN interface

* Select continue at each prompt until *Active Subscription Validation*

* Select Install CE

* Accept the default filesystem and partition scheme

* At the *ZFS Configuration* prompt, select stripe

* Select default options in the next prompts, the VM will reboot on its own

* Once pfSense boots, select *Set interface(s) IP address*

* Select the LAN interface

* Select *n* when asked to configure the interface via DHCP

* Enter *173.16.16.1* as the LAN's IPv4 address

* Enter 24 as the subnet bit count

* Select *n* when asked to configure interface via DHCP6

* Press enter to leave IPv6 address blank

* Select *n* when asked to enable DHCP server on LAN

* Select *n* when asked to revert to HTTP as the webConfigurator protocol

* Leave this VM on for the next VM setup

#### MS_AD
* Mount the Windows Server 2022 ISO 

* When setup begins, select Windows Server 2022 Standard Evaluation (Desktop Experience)

* Accept the license agreement

* Select Custom installation type

* Select Drive 0, the installation will now begin

* On first boot, enter the desired administrator password for this VM.

* On first login, open a PowerShell window with administrator privileges.

* Run the command Rename-Computer -NewName "$DESIREDHOSTNAME" -Force -PassThru

* Reboot the VM

* On next login, right click the network icon in the taskbar and select *Open Network & Internet settings*. 

* Select *Change adapter options* under Advanced network settings

* Select the Ethernet adapter

* Select Properties

* Select Internet Protocol Version 4 (TCP/IPv4)

* Fill in the following settings
     * IP address: 173.16.16.2
     * Subnet mask: 255.255.255.0
     * Default gateway: 173.16.16.1
     * Preferred DNS server: 1.1.1.1

* Confirm the settings

* When prompted about allowing other devices to discover this one, select Yes.

* Download (Git)[https://git-scm.com/downloads] using Microsoft Edge.

* Install Git with default options. 

* Open Git Bash

* Clone the project repository by running the command *git clone https://github.com/nniemeir/Enterprise-Computing-I*

* Return to the Internet Protocol Version 4 (TCP/IPv4) properties menu

* Select Obtain an IP address automatically. 

* Clear the Preferred DNS server section

* Confirm the settings

* Open a PowerShell prompt with administrative privileges and navigate to the directory Scripts\Microsoft\Active Directory Server under the cloned repository.

* Run the command *Set-ExecutionPolicy unrestricted -scope Process* to allow the deployment scripts to be run.

* At this point we can run scripts by typing .\SCRIPTNAME.ps1 

* Run the script AD_Deploy, this will install Active Directory Domain Services and add DNS records for each VM on the network as defined in DNS_Records.csv in the Records directory.

* You will be prompted to set a SafeModeAdministratorPassword, also known as the  Directory Services Restore Mode password.

* Choose A when prompted to confirm changes

* Run the script AD_Users, this will create privileged and unprivileged Active Directory users as defined in Users_Desktop_Admins.csv and Users_Developers.csv.
Temporary passwords for each created user will be generated and saved in the Reports directory.

* Follow the deployment instructions for MS_DevStation

* Now we can run the script AD_GPOs, this will apply my altered versions of the Microsoft Security Baseline GPOs to MS_AD and MS_DevStation;the reasons for these alterations are explained (here)[Preface.md].

* Reboot both Windows VMs at this point. 

* Open a PowerShell prompt with administrative privileges and navigate to the directory Scripts\Microsoft\Active Directory Server under the cloned repository. 

* Run the command *Invoke-Command -ComputerName PASSENGER01 -FilePath Dev_Toolkit.ps1*, this will install some common developer applications on MS_DevStation.

* Deployment of this network is now complete.

#### MS_DevStation

INSTALL INSTRUCTIONS OOBE\BYPASSNRO 

* On first login, download and install (Git)[https://git-scm.com/downloads]

* Clone the project repository by running the command *git clone https://github.com/nniemeir/Enterprise-Computing-I*

* Open a PowerShell prompt with administrative privileges and navigate to the directory Scripts\Microsoft\Development Workstation under the cloned repository.

* Run the command *Set-ExecutionPolicy unrestricted -scope Process* to allow the deployment scripts to be run.

* Run Pre_Enrollment, this will configure this device's network settings

* The VM will reboot on its own at this point

* Run Enrollment, this will join this device to the Active Directory domain. 

* The VM will reboot on its own again

* You will now be able to login to this device as any of the domain users we created, but you will login as the local user once more

* Run Post_Enrollment, this will add the privileged domain users to the local administrators group on MS_DevStation and configure Windows Remote Management so that scripts targeting MS_DevStation can be run from MS_AD.

* Return to MS_AD.

### Red Hat Network

#### RH_pfSense

* Select the first interface as the WAN interface

* Select the second interface as the LAN interface

* Select continue at each prompt until *Active Subscription Validation*

* Select Install CE

* Accept the default filesystem and partition scheme

* At the *ZFS Configuration* prompt, select stripe

* Select default options in the next prompts, the VM will reboot on its own

* Once pfSense boots, select *Set interface(s) IP address*

* Select the LAN interface

* Select *n* when asked to configure the interface via DHCP

* Enter *172.16.16.1* as the LAN's IPv4 address

* Enter 24 as the subnet bit count

* Select *n* when asked to configure interface via DHCP6

* Press enter to leave IPv6 address blank

* Select *n* when asked to enable DHCP server on LAN

* Select *n* when asked to revert to HTTP as the webConfigurator protocol

* Leave this VM on for the next VM setup

#### RH_FreeIPA
* Insert the Fedora Workstation 40 ISO into the VM's optical drive within the VirtualBox GUI

* Boot the VM

* Navigate to the pfSense web UI by typing its IP into the address bar, the current default login can be found in Netgate's documentation

* Select *Change the password in the User Manager*

* Enter a password of your choosing

* Select the save button at the bottom of the page

* Shutdown the VM

* Switch to the Fedora Server 40 ISO

* Boot the VM

* Select *User Creation* and make a user

* Ensure that your locale is correct in the Time & Date menu

* Select Network & Host Name, select Configure.., select IPv4 Settings, Set method to manual, select add, set the address to 172.16.16.2, the Netmask to 255.255.255.0, and the gateway to 172.16.16.1. Set the DNS servers to 1.1.1.1. Set the Host Name to station.universalnoodles.lan. Toggle the interface. CLick done

* Select Installation Destination, check *Encrypt my data* and *Free up space...* boxes and click done. Choose to delete all existing partitions. Enter an encryption passphrase when prompted. 

* Click begin installation 

* When the installation is complete, click Reboot System

* On first boot, update packages using the command *sudo dnf upgrade*

* Reboot the VM

* Install git by running *dnf install git*

* Clone the project repository by running *git clone https://github.com/nniemeir/Enterprise-Computing-I*

* Navigate to the directory Scripts/Red Hat/FreeIPA Server

* Run *chmod +x Pre-Deployment.sh Deploy.sh*, this will ensure that both deployment scripts are executable by the current user. 

* Run the script Pre-Deployment.sh, this will configure network settings and install some necessary packages. Reasons for installing these packages are explained (here)[preface.md].

* After some time, the VM will reboot on its own. 

* On first login to GNOME, open the terminal 

* Navigate to the directory Scripts/Red Hat/FreeIPA Server

* Run Deploy.sh, this will deploy FreeIPA itself.

#### RH_Ansible

* Insert the Fedora Server 39 ISO into the VM's optical drive within the VirtualBox GUI

* Boot the VM

* Select *User Creation* and make a user

* Ensure that your locale is correct in the Time & Date menu

* Select Network & Host Name, select Configure.., select IPv4 Settings, Set method to manual, select add, set the address to 172.16.16.3, the Netmask to 255.255.255.0, and the gateway to 172.16.16.1. Set the DNS servers to 1.1.1.1. Set the Host Name to conductor.universalnoodles.lan. Toggle the interface. Click done

* Select Installation Destination, check *Encrypt my data* and *Free up space...* boxes and click done. Choose to delete all existing partitions. Enter an encryption passphrase when prompted. 

* Click begin installation 

* When the installation is complete, click Reboot System

* On first boot, update packages using *dnf upgrade*

* Reboot the VM

* Install git by running *dnf install git*

* Clone the project repository by running *git clone https://github.com/nniemeir/Enterprise-Computing-I*

* Navigate to the directory Scripts/Red Hat/Ansible Server

* Run *chmod +x Enrollment.sh Post-Enrollment.sh*, this will ensure that both deployment scripts are executable by the current user. 

* Run the script Enrollment.sh, this will configure network settings and install some necessary packages before it enrolls the device as a ipa-client. Reasons for installing these packages are explained (here)[preface.md].

* Run the script Post-Enrollment.sh, this will install Ansible and generate SSH keys for this VM. 

* MORE ANSIBLE STUFF

#### RH_DevStation
* FEDORA WORKSTATION 40 INSTALL INSTRUCTIONS

* Open a terminal

* Navigate to the directory Scripts/Red Hat/Development Workstation

* Run *chmod +x Enroll.sh*, this will ensure that both deployment scripts are executable by the current user. 

* Run Enroll.sh, this will enroll the device as a ipa-client.

* MORE EXPLAIN
