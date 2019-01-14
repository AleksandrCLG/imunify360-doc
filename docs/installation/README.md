# Installation Guide
## Requirements
**Operating system**
* CentOS
* RHEL
* CloudLinux OS 6 and 7
* Ubuntu 16.04 LTS only <sup>3.6+</sup>.
* Ubuntu 18.04 <sup>Beta 3.9+</sup>.

**Virtualization**

OpenVZ - works for Virtuozzo 7 with kernel 3.10.0-327.10.1.vz7.12.8 or later.

**Hardware**

* RAM: 512Mb
* HDD: 20Gb available disk space

**Supported hosting panels**

* cPanel
* Plesk (Plesk 12.5 is not supported starting with Imunify360 3.8.5 Beta)
* DirectAdmin <sup>Imunify360 v. 3.1.3+</sup>.

_ISPManager, non-panel - soon after._

**Required browsers**

* Safari version 10 or later
* Chrome version 39 or later
* Firefox version 28 or later
* Edge version 17 or later

## Side by side installation with another IDS

**Compatible**

| | |
|-|-|
|**IDS name**| **Comment**|
|LiteSpeed | Integrates with version 5.1 or higher.|
|EasyApache3 | Works only in cPanel.|
|EasyApache4 | Works only in cPanel.|
|CSF | Integrated with CSF, more details [here](/ids_integration/#csf-integration).|
|CWAF Agent | No problems detected.|
|Patchman | No problems detected.|
|Suhosin | We are ignoring alerts by Suhosin.|
|Cloudflare | Starting from version 3.8, Imunify360 supports graylisting IP addresses behind Cloudflare. More details [here](/ids_integration/#cloudflare-support).|
|CXS | [Special actions required](/ids_integration/#cxs-integration) to use Imunify360 with CXS installed.|
|cPHulk | Imunify360 disables cPHulk during installation. However in case of enabling it back, Imunify360 integrates with it and shows cPHulk events in the incident screen.|
|OpenVZ | Works for Virtuozzo 7 with kernel 3.10.0-327.10.1.vz7.12.8 or later.|
|UptimeRobot| No problems detected.|

**Incompatible**

| | |
|-|-|
|**IDS name** | **Comment**|
|ASL (Atomicorp Secured Linux) | Possibly is not compatible (investigating).|
|fail2ban | Imunify360 disables fail2ban.|

## Installation Instructions

::: tip Note
Make sure that you have a license key. You can purchase it or get a trial license key at [https://www.imunify360.com/](https://www.imunify360.com/). Finally, you will receive an email with a license key.
:::
::: tip Note
Before proceeding to installation process read carefully the information about specific settings for each supported hosting panel and mod_security rulesets [here](/hosting_panels_specific_settin/).
:::
To install Imunify360 proceed the following steps:

1. Log in with root privileges to the server where Imunify360 should be installed.

2. Go to your home directory and run the commands:

```
wget https://repo.imunify360.cloudlinux.com/defence360/i360deploy.sh
```
```
bash i360deploy.sh --key YOUR_KEY
```

where `YOUR_KEY` is your license key. Replace `YOUR_KEY ` with the actual key - trial or purchased at [https://www.imunify360.com/](https://www.imunify360.com/) .

To install Imunify360 beta version add argument `--beta` . For example:

```
bash i360deploy.sh --key YOUR_KEY --beta
```

where `YOUR_KEY` is your license key. Replace `YOUR_KEY ` with the actual key - trial or purchased at [https://www.imunify360.com/](https://www.imunify360.com/) _._

If you have an IP-based license, run the same script with no arguments:

```
wget https://repo.imunify360.cloudlinux.com/defence360/i360deploy.sh
```
```
bash i360deploy.sh
```

To view available options for installation script run:

```
bash i360deploy.sh -h
```

In a case of registration key is passed later, then you can register an activation key via the Imunify360-agent command:

```
imunify360-agent register YOUR_KEY
```

Where `YOUR_KEY` is your activation key.

## Update Instructions



To upgrade Imunify360 run the command:

```
yum update imunify360-firewall
```

To update Imunify360 beta version run:

```
yum update imunify360-firewall --enablerepo=imunify360-testing
```

To update Imunify360 on Ubuntu run the command:

```
apt-get update
apt-get install --only-upgrade imunify360-firewall
```

To update Imunify360 beta version on Ubuntu the command:

```
echo 'deb https://repo.imunify360.cloudlinux.com/imunify360/ubuntu-testing/16.04/ xenial main'  > /etc/apt/sources.list.d/imunify360-testing.list
apt-get update
apt-get install --only-upgrade imunify360-firewall
```

If you do not want to receive updates from beta, remove beta repository:

```
rm /etc/apt/sources.list.d/imunify360-testing.list
apt-get update
```


