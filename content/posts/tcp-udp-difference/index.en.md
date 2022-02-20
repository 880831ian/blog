---
weight: 4
title: "Differences between TCP and UDP"
date: 2022-02-19T19:34:11+08:00
lastmod: 2022-02-19T19:34:11+08:00
draft: false
author: "PinYi"
authorLink: "https://feelit.khusika.com"
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["TCP", "UDP", "網路"]
categories: ["Network"]

lightgallery: true

toc:
  auto: false
---

Both TCP and UDP are common network communication protocols. These two protocols can ensure the speed and integrity of Internet data transmission. They operate in different ways. TCP is more reliable and UDP is faster.
Let's slowly analyze two different network communication protocols, what's so special!?

<!--more-->

## What is TCP 

TCP (Communication Control Protocol) is also the most commonly used protocol on the Internet. This protocol is more reliable and works as follows:

1.  TCP assigns a unique identification code and a sequence number to each packet. These numbers allow the receiver to identify the integrity of the packet and the sequence of the packets.
2. When the receiver receives the packet. If the sequence is correct, an acknowledgment signal is sent to the sender to confirm that the receiver has received the packet.
3. The sender will not transmit the next packet until the acknowledgment signal is received.
4. If the packet is lost or sent in the wrong order, the receiver will not transmit any information, which means that the sender needs to resend the packet.

## What is UDP

UDP can accomplish the same job without a unique identification code and serial number. This protocol already transmits data in a streaming manner, and because the sender does not return an acknowledgement signal, it will continue to send packets to the receiver. UDP is error-prone because it has no acknowledgment and doesn't care if packets are lost, but because it doesn't check, it's faster than TCP. Streaming software, online games, etc. all use this protocol.

##  TCP vs. UDP Comparison Chart

| Compare | TCP | UDP 
|:---- |:----:|:----:|
| Reliability | **Reliable** | Unreliable |
| Speed | Low | **Fast** |
| Transfer Method | Packets are transmitted in order | Packets are streamed |
| Whether to check for errors and corrections | **Have** | no |
| Applicable service content | Website browsing, e-mail, file browsing, requesting reliable transmission services | Instant service, online games, online live streaming |
