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

#### MS_AD

#### MS_DevStation


Lastly, return to MS_AD and run the script AD_GPOs.ps1

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

* Run the script Pre-Deployment.sh. After some time, the VM will reboot on its own.

* On first login to GNOME, open the terminal and run the script Deploy.sh.
GIVE DETAILS ON THIS

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

GIVE DETAILS ON THIS

#### RH_DevStation


