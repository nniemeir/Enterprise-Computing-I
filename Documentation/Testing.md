## Both Networks
| Item                                                                                       | Testing Method                                                                                                                                                                                                                                                                                                                                      |
|--------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| pfSense VMs can connect to the internet and allow devices in their subnet to do so as well | Pinging google.com from each pfSense VM’s CLI, then doing the same from each device in its subnet                                                                                                                                                                                                                                                   |
| Devices on LAN can communicate with each other                                             | Having each device ping every other device in its subnet                                                                                                                                                                                                                                                                                            |
| Both host and network-based firewall rules are being enforced                              | Attempting to circumvent each firewall rule                                                                                                                                                                                                                                                                                                         |
| DNS servers are functioning properly                                                       | Using each device to attempt to resolve the IP of every other device within its subnet.                                                                                                                                                                                                                                                             |
| Core functionality of developer applications intact after hardening                        | Attempting to utilize each of these applications as an unprivileged user on each of the development workstations                                                                                                                                                                                                                                    |
| Domain user accounts are working as intended                                               | For each user, attempting to: <br/><br/>- Log in to devices that they are not authorized to access<br/><br/>- Log in to devices that they are meant to have access to<br/><br/>- Perform actions that require permissions beyond what they have been granted on the devices that they are allowed to access<br/><br/>- Utilize admin or sudo rights if they have been granted them |
| Auditing is working as configured                                                          | Performing actions that would result in audit events while viewing logs to ensure that these events are being triggered as intended.                                                                                                                                                                                                                |
| The scripts and playbooks that have been made up to this point are functioning as intended | Running them again and checking that each task they are meant to accomplish has been done successfully                                                                                                                                                                                                                                              |
## Microsoft Network Only
| Item                                                                                                       | Testing Method                                                                                                                                     |
|------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| The Active Directory server can enforce group policies                                                     | Generating a Group Policy report on both the AD server and the development workstation and ensuring that all GPOs are being applied where intended |
| PowerShell remoting from the Active Directory server to the development workstation is working as intended | Attempting to run a script on the development workstation from the Active Directory Server via PowerShell remoting                                 |


## Red Hat Network Only
| Item                                                                       | Testing Method                                                                                        |
|----------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| The Ansible server can run playbooks targeting the development workstation | Attempting to run one of the playbooks targeting the development workstation from the Ansible server  |
| Intrusion Prevention System is working as intended                         | Attempting to trigger Fail2Ban to block the Parrot VM's IP by entering invalid credentials repeatedly |