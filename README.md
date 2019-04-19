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
systemctl enable iptables.service
systemctl enable iptables@base.service
systemctl start iptables@base.sercice
# To start another rules config files : 
systemctl enable iptables@docker-user.service
systemctl start iptables@docker-user.service
# Check status :
systemctl status iptables@base.service iptables@docker-user.service

```

## Configuration

Add - Edit - Remove rulebook from `/etc/iptables/` to allow a new sub-service to run.

Template included in the repository : 

  - [base.rules](etc/iptables/base.rules.empty)
  - [base.rules.empty](etc/iptables/base.rules)
  - [docker-user.rules](etc/iptables/docker-user.rules)
  - [docker-user.rules.empty](etc/iptables/docker-user.rules.empty)

### base.rules

Edit [base.rules](etc/iptables/base.rules) to add or edit the firewall restrictions.

We are using the `FILTERS` chain to add our rules.

### Exemple

_Open https port only for 1 ip/fqdn :_

    -A FILTERS -s 10.1.1.1/32 -p tcp -m tcp --dport 443 -j ACCEPT

_Open https port :_

    -A FILTERS -p tcp -m tcp --dport 443 -j ACCEPT

### Other rules

When we want to add another set of rules, we need to manually add an override on the new rule to set `Wants=iptables@base.service` as dependencie, then the new rules will be loaded after the base.

When we add another one, we need to map it to the second.

Exemple with docker-user.rules

#### docker-user.rules

Enable, start and configure the service :

```bash
systemctl enable iptables@docker-user.service
systemctl start iptables@docker-user.service
systemctl edit iptables@docker-user.service
```

Add to the override :

```conf
[Unit]
Wants=iptables@base.service
```

And save. You can now reboot safely without risking `COMMIT` error with `iptables-restore`.

------------------------------

Requirements : docker

Works with docker automatic rules creation by user `DOCKER-USER` chain. More informations on [Docker & Iptables](https://docs.docker.com/network/iptables/).

The [docker-user.rules](etc/iptables/docker-user.rules) rulebook is an exemple on how to limit docker port exposition on spécifique ip sources. We're using the `ctorigdstport` option with conntrack to restrict exposed ports.

Be carefull within every docker-user's rules, you have to set the external interface with `-i ens192` (change WAN interface name).

### Documentation

*  [iptables-restore man](http://manpages.ubuntu.com/manpages/bionic/man8/iptables-restore.8.html)
*  [iptables man](http://manpages.ubuntu.com/manpages/bionic/man8/iptables.8.html)
*  [docker/network/ptables](https://docs.docker.com/network/iptables/)
