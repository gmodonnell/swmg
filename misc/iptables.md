# iptables

iptables is a utility for managing and controlling netfilter, which is the firewall
utility on linux. Many people think iptables is just a firewall but it can also handle
NAT, modify packets and even act as a router.

iptables can filter inbound and outbound traffic. ufw, firewalld and netfilter all
use iptables under the hood to some extent.

## Tables

iptables uses tables and chains to manage traffic. A table is a collection of chains that
serves a particular function. There are five types of tables: *Filter*, *NAT*, *Mangle*,
*Raw* and *Security*. The *Security* table only exists on SELinux, so the other 4 are the
big ones you need to know about. Each table has a series of relevant chains:

| Filter Table | NAT Table | Mangle Table |
| --- | --- | --- |
| Input Chain | Output Chain | Input Chain |
| Output Chain | Prerouting Chain | Output Chain |
| Forward Chain | Postrouting Chain | Forward Chain |
| | | Prerouting Chain |
| | | Postrouting Chain |

The *Filter Table* is the firewalling table. The *NAT Table* helps you reroute connections to
other interfaces on the network (Literally NAT) and the *Mangle Table* helps you modify packets
and connections. The *Filter Table* is the most important one for security.

## Chains

Chains are tags that match packets to their state. For example, the input, output and forward
chains of the *Filter Table* would process packets going in, going out and being forwarded.
The input and output chains are the most relevant ones for the *Filter Table*. **They are**
**called chains because the rules are tested in sequence.**

### Prerouting and Postrouting

Prerouting chain is after packet has entered the network interface, but before it's
been sent out. Postrouting is after the destination has been decided, just before
the packet has left the network interface.

### Targets

If a match is hit in a chain rule, a *target* is set up. Targets are what you do with a
packet. Targets can be *accept*, *reject* and *drop*. Accept sends the packet on. Reject
will drop the packet and alert the user. Drop will just drop the packet without any
feedback.

## Using iptables

This is simply documentation reference. Remember to `sudo` if you aren't root!

* Flushing all iptables rules: `iptables -F`
* List all iptables chains for the current table: `iptables -L --line-numbers`
By default the *Filter Table* is used.
* Set a new default policy: `iptables --policy <CHAIN> <TARGET>`
* Insert new chain rule at top: `iptables -I <CHAIN> -s <SOURCE> -j <TARGET>`
* Append new chain rule at bottom: `iptables -A <CHAIN> -s <SOURCE> -j <TARGET>`
Sources may be single IP addresses, CIDR ranges or 0.0.0.0 (all)
* Delete chain rule: `iptables -D <CHAIN> <LN>` where LN is line number of rule.
* Protocol Rule: `iptables -(I/A) <CHAIN> -p (tcp/udp) --dport <portNUM> -j <TARGET>`
In this rule, `-p` specifies if the protocol is tcp or udp, and `--dport` specifies the actual
port number.

You can save rules with `sudo /sbin/iptables-save`
