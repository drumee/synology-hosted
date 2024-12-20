# Get started on Synology NAS
This documentation is intended to provide specific instructions to install Drumee Team on Synology NAS, Network Attached Storage. 

The installation package will use Debian Bookworm Docker Image in which all dependencies will be preinstalled and preconfigured to fit Drumee technichal reqirement, based on information you will have to provide through Synology Docker Manager Interface.

## Prerequisite

### Settings
- A maiden Internet domain name and all its subdomains
- Control Access to the GLU DNS of the Internet domain
- Control access to your Internet Router
- At least one fixed public IP addresses, IPV4 and/or IPV6

### Network Capabilities
If you plan to install your Drumee Server behind an Internet Box, ensure that it has following capabilities:
- NAT (Network Translation Address)
- Fixed public IP address
- DHCP settings
- DNS settings

### Synology
#### Operating System
- DSM 7.0 or higher
- At least one volume dedicated to Drumee usage

#### Hardware
- RAM at least 8Go
- CPU at leat 2 GHz
- Enough space to host what you need 

### Recommandations
- Drumee should be installed on a dedicated folders
- MFS (/data) should be not installed on the same partition as server (/srv)
- If you expect high rate of read/write operations, database base partition (/srv/db) should be installed on a high speed device.

### Caution
- The provided domain name can not be shared with existing or futur application
- It is recommanded not to share DB server with any other application

## Installation
### Prepare your ISP settings

#### Prepare you IP addresses
This task depends on your Domain Name Provider. Replace *example.org* by your own domain name. If you don't have IP V6 address, just fill in IPV4 fields.
The purpose of this task to bind your domain name to the Ip Address of your Synology Box. You will have to create following records in your Domain Name Provider:

  | Domain Name      |  Type  | Target             |
  |------------------|--------|--------------------|
  | example.org      | A      | your.ip.4.address  |
  | example.org      | AAAA   | your.ip.6.address  |
  | ns1.example.org  | A      | your.ip.4.address  |
  | ns1.example.org  | AAAA   | your.ip.6.address  |
  | ns2.example.org  | A      | your.ip.4.address  |
  | ns2.example.org  | AAAA   | your.ip.6.address  |

#### Change the default Domain Name Server (DNS)
This section will replace your ISP DNS by the one that run on your own Synology NAS. To make this effective, open your ISP interface and change current Name Servers to ns1.example.org and ns2.example.org

#### Change your GLUE DNS
Open your ISP interface and add following entries:
- ns1.example.org 
- ns2.example.org

### Prepare your Internet Router
If your NAS is not inside a private network or you don't need address translation or ports mapping, you can skip this section and jump to [Prepare your Drumee Volumes](#prepare-your-drumee-volumes)

- Refer to you ISP to know how to change your network settings
- Log into you Internet Router
- Check your IP addresses. Keep them noted. You may have IPV4 and IPV6. At least one of them is reuired. They must be public, i.e reachable from Public Internet.
- Open the ports or network manager and add NAT rules as follow

  | LAN   |  WAN  | Type    |
  |-------|-------|---------|
  | 60000 | 10000 | udp     |
  | 4353  | 53    | tcp/udp |
  | 6443  | 443   | tcp     |
  | 4080  | 80    | tcp     |
  | 6222  | 5222  | tcp     |

Note: Values in WAN column must be exactly the same. The ones in LAN column may be changed to comply your own environment. Keep these values noted. You will need them later to configure create Drumee Container.

### Prepare your Drumee Volumes
#### Basic knowledge
Refer to Synology Documentatio to [create a volume](https://kb.synology.com/en-my/DSM/help/DSM/StorageManager/volume_create_volume?version=7) or watch video tutorials, [for example this good one](https://youtu.be/iR3BqL0_7J4), but there are plentyful of them. Just pick the one you are easy with.

Once the volume(s) created, create two Synology "Shared Folders". These folders must not be used by other applications or users. If you are confotable enough with your drives and you plane to have an extensive usage, it is recommended to have two dedicated volumes.

#### Create Shared folders
Below instruction is detailed as [a short video here](https://youtu.be/HZx4fbfv65E)

- From the main Interface Open the control Panel
- From the Control Panel, open Shared Foder
- From the topbar menu, click create -> Create Shared Folder
You will have to create two Shared Folders with the name of your own preference. It's recommended to prefix them, so that it will be easy to know what kind of data are being stored. For example : 
- drumee-data for Drumee Filesystem Storage 
- drumee-db for Drumee Database Storage
![Create shared folder](https://github.com/drumee/synology-hosted/blob/main/images/create-shared-folder-2.png)


## Prepare Drumee Container from a template file
This is the simplest way to setup container. But if you are not comfortable using file editor, jump to [Prepare Drumee Container from Container Manager](#prepare-drumee-montainer-from-montainer-manager).
- Download the template from [here](https://github.com/drumee/synology-hosted/blob/main/template.json)
- Edit the file by setting you own values in following sections:
- [env_variables](https://github.com/drumee/synology-hosted/blob/main/template.json#L10)
- [port_bindings](https://github.com/drumee/synology-hosted/blob/main/template.json#L52)
- [volume_bindings](https://github.com/drumee/synology-hosted/blob/main/template.json#L134)
- Save the file
- Open the Container manager
- Select Action -> Import -> From Local Device
- Once the template imported, select the container, then click Action -> Start

[Watch video here](https://youtu.be/8RQWJMeMj6k)

The installation has started. Jump to [Monitoring Installation Progress](#monitoring -installation-progress)


### Prepare Drumee Container from Container Manager
Below instruction is detailed as [a short video here](https://youtu.be/NM1gJUQ2bmw)

- From Synology main page, open Package Center. 
- From Package Center, open Docker Manager. 
- From Docker Manger, open Conatiner
- On the right panel, click the button "Create"
- From the General settings, click images dropdown
- From the dropdown click "Add Image"
' From the Download image popup, type drumee in the searchbox
- From the results list, select "drumee/stable" and download
- Optionnaly give the name you want to the conatiaber
- Enable auto-restart option and click next
  ![create shared folder](https://github.com/drumee/synology-hosted/blob/main/images/create-container-2.png)

#### Port settings
Before you start, refer to the port settings you have done on your [Internet Router](#prepare-your-internet-router). Have them in a way you can copy/paste easily. 
- In Port Settings block, click the button +Add. You will have to do this for each port. Copy the value from the column WAN and paste it into the box Container Port. Select the right port from the dropdown.
![Configure shared folder](https://github.com/drumee/synology-hosted/blob/main/images/port-settings.png)

#### Volume settings
- In the Volume Settings, click +Add Folder
- From the popip, select the shared folder you've created:
  * Next to the shared folder drumee-data, type /data
  * Next to the shared folder drumee-db, type /srv/db
![Configure shared folder](https://github.com/drumee/synology-hosted/blob/main/images/volume-settings-2.png)

#### Environment
Refer to the list of [Environment Variables](https://github.com/drumee/debian-hosted/blob/main/env.sh) to understand their purpose
Click the button +Add. You will have to do this for each variable as below. 
- DRUMEE_DESCRIPTION
- DRUMEE_DOMAIN_NAME
- PUBLIC_IP4
- PUBLIC_IP6
- ADMIN_EMAIL
- ACME_EMAIL_ACCOUNT
![Configure shared folder](https://github.com/drumee/synology-hosted/blob/main/images/env-settings.png)

Once everything is done, click Next. The installation has started.


#### Monitoring Installation Progress
To check that everything is running as expected, open the container terminal.

- In the containers list, select Drumee Container. Click Action -> Open the terminal. 
![Open Terminal](https://github.com/drumee/synology-hosted/blob/main/images/open-terminal.png)

Once the installation completed, you will receive a link sent to the *ADMIN_EMAIL*. Click on the link and set the admin pass word. That's it !

