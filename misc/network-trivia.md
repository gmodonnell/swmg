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

## The Following Images are IP of HackTheBox

<figure><img src="https://academy.hackthebox.com/storage/modules/34/redesigned/net_models_pdu2.png" alt=""><figcaption><p>The Open Systems Interconnection, Transmission Control Protocol  and Protocol Data Unit Models</p></figcaption></figure>



<figure><img src="https://academy.hackthebox.com/storage/modules/34/packet_transfer.png" alt=""><figcaption><p>Each layer of the OSI adds a header to the PDU to help route each packet</p></figcaption></figure>
