# ansible-role-iptables
This role configures iptables for IPv4 on Debian systems. Additional to user-specified rules, it installs a script to get the latest DROP list from spamhaus.org/drop/ and writes it to a separate spamhaus-droplist chain.

## Default rules
There are some default rules configured. Take a look at the rules.v4 template.

## Rules example
```
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
Packets with a source address matching one of the IPs from this list, are going to be dropped and logged to syslog.
```
iptables_blocklist:
  - 10.10.10.0/24
```
## spamhaus-droplist
The spamhaus-droplist feature is enabled by default. Dropped packets by its rules are logged to syslog.
```
iptables_enable_droplist: true
```
