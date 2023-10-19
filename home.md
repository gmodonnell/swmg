---
title: Home
description: 
published: true
date: 2023-10-19T21:44:23.761Z
tags: 
editor: markdown
dateCreated: 2023-10-19T21:44:23.761Z
---

# Attack Notebook

## How to Use this Notebook

I wrote this document so network tests are less stressful. Methodology and tools I use for each step are included top to bottom. Each topic is covered in the order they are supposed to appear, so ideally when you are at the bottom of the document you should have DA.

Here is the general intended flow of an attack using this notebook:

Insert image here with flowchart as follows: See new endpoint -> enumerate -> gain foothold -> \[loop: privesc and post-exploitation] -> pivot. If you hit a wall while doing privesc, check the post-exploitation section to see if you have any sort of important access to anything useful given the permissions you do have.&#x20;

Note that 'foothold' does not mean a foothold into a network, but rather a foothold onto a given endpoint. You will iterate through this document every time you encounter a new endpoint.

{% hint style="danger" %}
**DISCLAIMER.** All information contained in this blog is provided for educational and research purposes only. The author is not responsible for any illegal use of any information published on the pages of this blog.
{% endhint %}
