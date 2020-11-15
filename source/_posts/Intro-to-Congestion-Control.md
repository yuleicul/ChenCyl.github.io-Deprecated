---
title: 「阅读笔记」Intro to Congestion Control
date: 2019-01-07 17:38:09
index_img: /assets/tcp-tahoe-reno.png
excerpt: 介绍拥塞控制中的「慢开始和拥塞避免」算法及其弊端
updated: 2019-01-07 17:38:09
categories: 阅读笔记
tags: Algorithm
---

> 原文：http://squidarth.com/rc/programming/networking/2018/07/18/intro-congestion.html

 本文主要介绍拥塞控制中的「慢开始和拥塞避免」算法。慢开始是刚指开始的拥塞窗口很小，从 1 开始，随着传输轮次的增加拥塞窗口呈指数增长，当拥塞窗口等于 ssthresh 时，变为线性增长，直到检测到拥塞（丢包），窗口变 1，ssthresh 变为拥塞时的一半。另外，作者还分析了这个算法的缺点。

## Extracts

**It’s worth noting too that** the protocol that TCP works on top of, IP, operates by transmitting packets with a maximum size of 1500 bytes. So if a sender needs to send 100kb, they need to **chop up** that data into segments, send them over TCP, and receive acknowledgements for ==all== of those segments.

**It’s also worth noting here that** senders do not know up front what the properties of the links that they are sending information on are. If you request the website “http://www.google.com”, for instance, the packets you send are probably going through many links before finally getting to Google’s servers, and the rate at which you can send information is fundamentally going to be ==clamped== by the slowest link on that route. （短板效应）

### Like Pipe

Using the **analogy** of the link as a ==pipe==, you can think of the *delay* as the ==length== of the pipe, and the *bandwidth* as the ==circumference== of the pipe. An important statistic about a link is the bandwidth-delay product (BDP). This can be thought of the ==volume== of the pipe in the pipe analogy. A link is *fully **utilized*** when the number of bytes flowing through it is equal to the BDP. 

### Indicators

What indicators do senders have that congestion is happening? There are two main indicators: ==packet loss== and ==increased round trip times== for packets. There are two ways that a TCP sender could detect that a packet is lost. ==Detecting Packet Loss== & ==Fast Retransmit==.

Fast Retransmit: The receiver sends back “duplicate acks”. In TCP, receivers only acknowledge packets that are sent in order. If a packet is sent out of order, it will send out an acknowledgement for the last packet it saw in order. So, if a receiver has received segments 1,2, and 3, and then receives segment #5, it will ack segment #3 again, because #5 came in out of order. In Tahoe, if a sender receives 3 duplicate acks, it considers a packet lost. This is considered “Fast Retransmit”, because it doesn’t wait for the timeout to happen.

### Congestion Window

 If the ==congestion window== on a sender is set to `2`, that means that after the sender sends `2` segments, it must wait to get an acknowledgment from the receiver in order to send any more. The congestion window is often referred to as the “flight size”, because it also corresponds to the number of segments “in flight” at any given point in time.

（发送窗口 = min { 接收窗口， 拥塞窗口}，假设接收方有足够大缓存空间及接收窗口足够大，则发送窗口 == 拥塞窗口）

### Issues

There are a number of issues with this approach though, which is why it is no longer used today. In particular, it takes a ==really long time, especially on higher bandwidth networks==, for the algorithm to actually take full advantage of the available bandwidth. This is because the window size grows pretty slowly after hitting the slow start threshold.

Another issue is that ==packet loss doesn’t necessarily mean that congestion is occuring==–some links, like WiFi, are just inherently lossy. Reacting drastically by cutting the window size to 1 isn’t necessarily always appropriate.

A final issue is that this algorithm uses packet loss as the indicator for whether there’s congestion. ==If the packet loss is happening due to congestion, you are already too late==–the window is too high, and you need to let the queues drain.

![tcp-tahoe-reno](/assets/tcp-tahoe-reno.png)

## Words (adv.)

### collectively 

> In order for both senders to finish sending the information that they are sending, they need to **collectively** **reduce** the amount of information that they are sending.

为了防止 congestion 让两个 senders 都顺利地完成信息的发送，他们需要集体减少信息的数量。

### fully

> A link is **fully utilized** when the number of bytes flowing through it is equal to the BDP.

### fundamentally

> For instance, the packets you send are probably going through many links before finally getting to Google’s servers, and the rate at which you can send information is **fundamentally going to be clamped** by the slowest link on that route.

基本上/根本上是被最慢的链接堵塞住的。

### intuitively

> To understand this **intuitively**, ...

直观地理解这一点

### fairly

> The algorithm itself is **fairly** simple.

相当地简单

### effectively

> This **effectively** doubles the congestion window on every round trip. 

这有效地使每次往返的拥堵窗口加倍。

### accordingly

> For instance, if another sender starts sending packets on the same link, it will reduce the bandwidth available, and the sender will need to adjust **accordingly**. If another sender on the same link stops sending packets, the sender will have more bandwidth available, and in order to utilize it, will need to adjust **accordingly**.

### drastically & necessarily

> Reacting **drastically** by cutting the window size to 1 isn’t **necessarily** always appropriate.

**剧烈地**反应为...并不一定总是**必然地**合适的。

### barely

> the Tahoe sender barely gets any bytes through

几乎没有；仅仅