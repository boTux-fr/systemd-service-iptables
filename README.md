# Systemd Iptables Rules Management

*Persistent iptables rules with multi-instantiated iptables rulebooks.*

Edit, load, change iptables rules whith systemd service using _iptables-save_ rules formating.

Start, restart, reload, and stop firewall with systemd service.

## System requirements

*  iptables
*  systemd

## Instalation
On the linux system you wish to install the firewall sercice, run as root : 

```bash
mkdir /opt/security/ && cd /opt/security
git clone 
cd systemd-service-iptables/
# Edit the rules in etc/iptables/base.rules as needed.
# and install the service
cp -Rv etc/. /etc/
systemctl daemon-reload
systemctl enable iptables@base
systemctl start iptables@base
# To start another rules config files : 
systemctk start iptables@docker-user
```

## Configuration

Edit [base.rules](etc/iptables/base.rules) to add or edit the firewall restrictions.

We are using the `FILTERS` chain to add our rules.

### Exemple

_Open https port only for 1 ip/fqdn :_

    -A FILTERS -s 10.1.1.1/32 -p tcp -m tcp --dport 443 -j ACCEPT

_Open https port :_

    -A FILTERS -p tcp -m tcp --dport 443 -j ACCEPT

### Documentation

*  [iptables-restore man](http://manpages.ubuntu.com/manpages/bionic/man8/iptables-restore.8.html)
*  [iptables man](http://manpages.ubuntu.com/manpages/bionic/man8/iptables.8.html)
