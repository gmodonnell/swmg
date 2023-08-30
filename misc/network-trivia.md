---
description: Things you may or may not have known!
---

# Network Trivia

## VPNs

There are multiple types of VPNs!

{% tabs %}
{% tab title="Site-To-Site" %}
Site-To-Site is used to join networks, and both the client and server of the VPN are generally networking devices (routers, firewalls, etc)
{% endtab %}

{% tab title="Remote Access" %}
Client generates a virtual interface that interacts with data as if it were on the client's network. `OpenVPN` does this with the `tun0` interface. If the VPN only creates routes for a specific network it's considered a `Split-Tunnel VPN` which means the connection is not going out of the VPN. This is good because your internet connection cannot be monitored but network based detection methods for malware will fail.&#x20;
{% endtab %}

{% tab title="SSL VPN" %}
SSL VPNs are browser based. They are used to stream applications or entire desktop sessions to the web browser. Browser-based RDP uses an SSL VPN
{% endtab %}
{% endtabs %}

TCP 1723 is generally used for Point-to-Point Tunneling Protocol (PPTP) and UDP 500 is generally used for IKEv1 and IKEv2. These are common VPN connection protocols. VPNs typcially use Encapsulating Security Payload (ESP) to encrypt VPN traffic. PPTP is deprecated.&#x20;

## Proxies

There are multiple types of proxies!

{% tabs %}
{% tab title="Dedicated/Forward Proxy" %}
Forward proxies receive requests from the client and submit them to the greater internet on behalf of the client. This prevents a computer from having direct access to the internet. Filters outgoing requests.
{% endtab %}

{% tab title="Reverse Proxy" %}
Filters incoming requests. Reverse proxies listen on an address and foward the requests into a closed network. CloudFlare and WAFs are examples of Reverse Proxies because they monitor the incoming requests and prevent certain ones from making it into the network.&#x20;
{% endtab %}
{% endtabs %}

Proxies can work transparently or non-transparently. Transparent proxies exist without the client's knowledge, but non-transparent proxies require the client to set up the connection.&#x20;

## Packet Routing

Images are IP of HackTheBox

<figure><img src="https://academy.hackthebox.com/storage/modules/34/redesigned/net_models_pdu2.png" alt=""><figcaption><p>The Open Systems Interconnection, Transmission Control Protocol  and Protocol Data Unit Models</p></figcaption></figure>



<figure><img src="https://academy.hackthebox.com/storage/modules/34/packet_transfer.png" alt=""><figcaption><p>Each layer of the OSI adds a header to the PDU to help route each packet</p></figcaption></figure>

## MAC Addresses

48 bits, the first 24 bits tell you the hardware manufacturer. The last bit of the first octet tells you if the MAC address is `unicast (0)` or `multicast (1)`. The MAC will be all 1's if it is a Broadcast device. The second to last bit of the first octet tells you if the MAC address is a `global OUI (0)` or `Locally Administrated (0)`. ARP is what resolves IPs to MACs.

## IPv6

Has a Network Prefix and an Interface Identifier. 128 bits long. The Interface Identifier is also called the suffix and is created by converting the 48 bit MAC into a 64 bit address.

{% embed url="https://datatracker.ietf.org/doc/html/rfc5952" %}
IPv6 Notation Explained
{% endembed %}

## VLANs

You can filter VLAN traffic from a wireshark capture with the `vlan` filter. This is becuase all VLAN packets are shipped with a VLAN identification number. You can also do this in `tshark`:

```bash
tshark -r "capture.pcapng" -T fields -e vlan.id | sort -n -u
```

Additionally, you can hop and sniff other VLANs if Cisco's Dynamic Trunking Protocol (DTP) is enabled and you can physically access the switch with those settings.&#x20;

{% hint style="info" %}
There is a double-tagged VLAN hopping attack you can perform with Scapy or Yersinia that allows you to send data from one VLAN to a different VLAN.&#x20;
{% endhint %}

{% embed url="https://datatracker.ietf.org/doc/html/rfc7348" %}
Data Centers us VXLAN&#x20;
{% endembed %}
