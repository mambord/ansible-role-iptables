# ansible-role-iptables
This role configures iptables for IPv4 on Debian systems. Additional to user-specified rules, it installs a script to get the latest DROP list from spamhaus.org/drop/ and writes it to a separate spamhaus-droplist chain. The docker-iptables feature lets you create a whitelist ruleset and appending droplists for docker container access.

## Default rules
There are some default rules configured. Take a look at the rules.v4 template.

## Rules example
```yaml
iptables_rules:
  - rule:
    chain: INPUT
    interface: eth0                         (optional)
    protocol: tcp
    source: 192.168.1.0/24                  (optional)
    destination_port: 2244
    match: state                            (optional)
    match_options: --state new              (optional)
    jump: ACCEPT
  - rule:
    chain: INPUT
    protocol: tcp
    destination_port: 22
    jump: ACCEPT
```
## blocklist example
Packets with a source address matching one of the IPs from this list, are going to be dropped and logged to syslog (tagged with ``[USER BLOCK]``).
```yaml
iptables_user_droplist:
  - 132.232.0.0/16
  - 58.242.0.0/16
```
## spamhaus-droplist
The spamhaus-droplist feature is disabled by default. Dropped packets by its rules are logged to syslog (tagged with ``[SPAMHAUS BLOCK]``).
```yaml
# enable spamhaus droplist feature
iptables_enable_spamhaus_droplist: false
```

## docker-iptables
The docker-iptables feature lets you manage source acl's for accessing docker container. Rules are written with a script ``/usr/local/sbin/docker-iptables`` as systemd service after docker started. The feature is enabled by specifying rules:

```yaml
iptables_docker_source_acl:
  - rule:
    interface: eth0
    protocol: tcp
    source: 192.168.168.0/24
    destination_port: 80
```

``/usr/local/sbin/docker-iptables`` creates a chain, default named ``DOCKER-USER``, and inserts it into the FORWARD chain before all docker managed chains. Rules defined in ``iptables_docker_source_acl`` create negated rules as explained at (https://docs.docker.com/network/iptables/).


```yaml
# docker user specified rules chain name
iptables_docker_chain: 'DOCKER-USER'
```

### droplists
Additionally the spamhaus- and user-droplists can be activated for the FORWARD chain. They are disable by default:
```yaml
# enable user droplist for docker host
iptables_enable_user_droplist_docker: false

# enable spamhaus droplist for docker host
iptables_enable_spamhaus_droplist_docker: false
```
