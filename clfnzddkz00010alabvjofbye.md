---
title: "Diving into Proxies: Connection Termination!"
seoDescription: "This article talks about connection termination"
datePublished: Sat Mar 25 2023 13:01:53 GMT+0000 (Coordinated Universal Time)
cuid: clfnzddkz00010alabvjofbye
slug: diving-into-proxies-connection-termination
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1679749160964/00a8970e-a269-419c-9617-a61ce0605c8a.jpeg
tags: learning, devops, networking, proxies

---

Hello everyone, it's been a while since I've written anything since I'm always busy. So this article will explain a simple concept that many fail to recognize and I did too.

The concept I'm talking about is called "Connection Termination", there could be various names for this. I recall hearing about this when I was working on writing the Developer guide for Ingress Nginx, I knew what it meant but never knew the term for it.

In a nutshell "Connection Termination" happens when you interact with Proxies(Even an AWS application load balancer is a reverse proxy), I know of three proxies, Envoy, Nginx, and HaProxy.

The image below shows you what I mean when I say connection termination(No this doesn't mean you're disconnected from the proxy)

![A diagram that shows how connection goes from your computer to the proxy and the proxy then initates a new connection to the upstream application.](https://cdn.hashnode.com/res/hashnode/image/upload/v1679748236430/f2116b0c-28be-4e28-8d20-1d89aa034a28.png align="center")

Proxies are used to route connections to the actual applications using a single entry point. More often these are called "Reverse Proxies". When you initiate a connection to a reverse proxy your connection doesn't go directly to the application, the proxy will create a new connection to the actual service, this is one of the reasons why you'll see the Proxy IP in the application logs and not the client's actual IP.

This is quite useful as it allows you do to a lot of things, for example, you can add headers to the request, or maybe you want to rate-limit a request.

In Envoy Proxy you have something called Filter Chains these chains can be run when a request reaches the proxy, things like using an external authentication server, rate limiting, adding a wasm filter, modifying a request header, or adding headers.

A few things that most people ask on forums, discord, and most of the places is how do I get the actual client IP in the application logs, To be honest, this isn't needed since you can use the Proxy Logs, but sometimes you have CloudFlare in front of your application, Cloudflare is also a Reverse Proxy which connects to your application. You can instruct Cloudflare to send the IPs using the headers it provides.

This piece of information is really helpful when you're debugging as a single connection can be terminated at multiple places during its lifecycle. Figuring out where the termination occurs helps debug microservices.

A small homework from this Article is to search the headers that Cloudflare or any proxy uses to send the actual client IP.

Until Next time! I wish I could write more frequently but I'm bad at a lot of things including time management. I hope this helps you debug your issues faster!