---
title: setup
description: Setting things up
published: true
date: 2023-12-21T20:29:25.346Z
tags: 
editor: markdown
dateCreated: 2023-10-19T16:29:20.411Z
---

# Setup

## Burp Suite Setup

When working on a team, setting up a lab environment is the ideal method for working together. This is convenient for the client's Blue Team, as they only have to worry about a single IP Address from which all traffic related to the ongoing test will originate.

When working through a lab, I have to ssh into the box and use my tools through the CLI. This generally isn't an issue, but if I need to use BurpSuite, the problem of not having a GUI gets in the way. To combat this, I use Dynamic Port Forwarding to forward my BurpSuite traffic through the EC2 instance I have an ssh connection with. Here's how to do it:

Open BurpSuite, go to Project Options (or user options for a permanent change which I don't recommend). Under SOCKS proxy, enable everything and run the SOCKS proxy address as 127.0.0.1. _you can't put localhost_. Next you set the port to whatever you want your port to be. SSH into the box, and set the -D flag with your port matching the port you indicated in BurpSuite. Activate your Burp Proxy as usual. The traffic will flow from the browser through burp to your EC2 out into the world.

## Forwarding

Local port forwards are a good way to ensure a connection takes an unorthodox route if you're dodging firewalls or routing tools through an access point on an internal network. Local port forwards will take outgoing data from a local port, and connect it to a different port on the target box. Here is the general format:

> ```
> ssh -N -L [port]:localhost:[port] user@domain
> ```

using `-N` prevents ssh from running a command or opening a shell, but will forward the port. You can do this through multiple layers of network, for example your device touches a VPS that exists between you and a target network. If you have access to a device on that network, you can push traffic from the victim device all the way down to the screen in front of your keyboard.

> ```
> ssh -N -R [port]:localhost:[port] user@domain
> ```

Above is a remote port forward, which will set the first port on a remote device to accept connections and forward them to the second port on the remote device.

* Plant a device on the target network. Set a reverse port forward on it to reach back to your lab environment.
* Set a local port forward from the client device to the lab environment.
* The data will now kiss in the middle.

Dynamic Port Forwards let you push all data from a client through to the target machine via a single port. Once it gets to the target device, the packets are then sorted to local ports. Can break.

> ```
> ssh -D [port] user@domain
> ```

I don't X-11 forward for GUI applications because I don't know enough about it to run it securely.

### BurpSuite Extensions
You should have the following extensions installed on BurpSuite if you're going to be using it for any kind of serious work:

* 403 Bypasser
* Active Scan ++
* Autorize
* Freddy Deserialization Finder
* GraphQL Raider
* InQL - GraphQL Scanner
* Java Deserialization Scanner
* JWT Editor
* Log4Shell Everywhere
* Log4Shell Scanner
* PDF Viewer
* Retire.js
* Site Map Extractor
* ViewState Editor
* .NET Beautifier

## \*THAT\* Forward

The one that is always messing you up. You should put it somewhere more convenient later, or maybe just the act of recording it one final time will actually make the process stick.

```
====================         ====================         ====================
|    Attacker      |   -->   |       Proxy      |   -->   |    Internal Env. |
====================         ====================         ====================
```

| Device   | Command                                       | Reason                                                                               |
| -------- | --------------------------------------------- | ------------------------------------------------------------------------------------ |
| Attacker | `ssh -L 8888:localhost:8888 proxyuser@proxy`           | Connect to proxy, bring port forward down to attacker                                |
| Proxy    | `ssh -p 2222 -D 8888 internaluser@internal`   | Complete chain as middle link. Become SOCKS5 proxy for internal machine and attacker |
| Internal | `ssh -N -R 2222:localhost:22 proxyuser@proxy` | Callback from Internal machine to proxy                                              |

## Git

```
ssh-keygen -t ed25519 -C "email@email.dom"

eval "$(ssh-agent -s)"

ssh-add ~/.ssh/[keyfile]
```

Then add your public key to the website.

```
git clone git@github.com:/[author]/[repo]
```

### Set-URL

When you clone over https but need to push a commit later

```
git remote set-url [remote name] [remote url]
```

The `[remote name]` is usually `origin` or `main`, and the `[remote url]` is in the format `git@github.com/[author]/[repo]`.

## Vim Shortcuts

```
Toggle Line #
:set number
:set nonumber

Search Forward/Backward (pan with n):
/<term> 
?<term> 

Append * or Delete char from every EOL:
:%norm A*
:%norm $d x

Cut Word/Line, Copy Word/Line:
dw dd yw yy
```

* [Vim Cheat Sheet](https://vimsheet.com/)