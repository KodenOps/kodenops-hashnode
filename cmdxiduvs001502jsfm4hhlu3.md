---
title: "Content Delivery Network (CDN)"
seoTitle: "Content Delivery Network (CDN)"
seoDescription: "When content is present everywhere you go"
datePublished: Mon Aug 04 2025 19:34:03 GMT+0000 (Coordinated Universal Time)
cuid: cmdxiduvs001502jsfm4hhlu3
slug: content-delivery-network-cdn
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/8bghKxNU1j0/upload/b0572fda411fb4cb6e0a594ec243a8c3.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1754335934626/35139899-d639-4d14-a301-6f7ae9726ac6.png
tags: cdn, technology, devops, system-design, technical-writing-1, content-delivery-network

---

## Introduction

Woooops! Here we go again with another big tech grammar. Tech is just filled with different acronyms that confuses our mind.

Don’t worry, this one is not as crazy as it sounds. In fact, it even described how it help both clients and developers in ensuring their experience on the website is top notch.

Before we go deeper into what this actually means, let’s see this illustration together.

We are familiar with TEMU ecommerce platform where we can get cool (and not so cool) products. They have different product that stems different categories. From fashion to household appliances. But one (or two if we consider the “what i ordered vs what i got problem) thing always put us off partially about buying from them. Longer Delivery Time to Nigeria! Imagine, i have to wait for roughly two weeks for me to get my items.

**Why is this so?**

TEMU is not a Nigerian Company. In fact, they have no branch in Nigeria or Africa as a whole. So, whatsoever item i am buying from them have to be shipped from China to Nigeria. This is a long distance which explains the reason behind the longer delivery time.

This could have been reduced if they have warehouses in different regions or countries. This way, items will be shipped from a warehouse closer to the buyer location thereby reducing the delivery time significantly to 1-3 days.

How will you feel as buyer about this?

This is exactly what Content deliery Network does.

## What the Heck is now CDN?

Content delivery network as the name suggest is a network/chain/connections/links/etc that facilitate the way web content (HTML pages, JavaScript files, stylesheets, images, and videos) are delivered. And to whom? Users of course. One of the frustration user faces online is the load rate of pages and content. Imagine spending 30 minutes to watch a Youtube video that is 5 minutes long. All because the video keeps buffering repeatedly. With the introduction of CDN, static contents are distributed across different region (Edge locations) closer to the user such that when they try to load a page, instead of fetching from the main source (let’s say China), they will be fetching from a CDN location closer to them (let’s say Lagos). That way, the page loads faster with lower latency.

Is this not the same as **Web Hosting?**

Well, even though what both of them does is to store website content. CDN stores only the static contents such as HTML pages, JavaScript files, stylesheets, images, and videos that might take a longer time to load in different location. It then distribute it across different geo-locations. But web hosting on the other hand stores the entire website or application, including backend logic, databases, and dynamic content. By default, Web hosting typically resides in one or a few data centers. CDNs, on the other hand, push static assets to many edge locations around the world to serve users from the closest point.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754333676780/bedc7675-8358-4c30-8035-ee55dcec4a58.png align="center")

## Types of content delivered by CDN

Apart from static contents like HTML pages, images, videos, Javascript files, etc., CDN also:

* [**Cache**](https://kodenops.hashnode.dev/caching-deliver-content-faster-than-bolt) **dynamic content** (with rules)
    
* **Accelerate APIs** and **real-time data**
    
* **Stream video content** (adaptive bitrate streaming)
    

## How CDNs Handle Content

We’ve discussed how application static contents are being distributed across regions. But how does this happens? This is majorly done by caching. Edge servers cache static content based on rules set by the origin server, such as cache-control headers or CDN configuration settings.

The application owner can define the Cache rules - what should be cached, how long should it be cached (TTL - Time to leave), how cache should be purged and [invalidated](https://kodenops.hashnode.dev/caching-deliver-content-faster-than-bolt#heading-other-key-concept-involved-in-caching).

Modern CDNs (like Cloudflare, AWS CloudFront, Fastly) allow you to:

* Run **custom code at the edge** (e.g., A/B testing, geo-based redirection, bot filtering). So, if you want to route request from a particular region to a particular edge server, you can create the logic here also.
    
* Implement **security headers**, authentication checks, etc.
    

At its heart, a CDN is **more than just replication**. It’s a **performance, reliability, and security layer** that lives close to the user.

## Importance of CDN

1. ### ⚡ **Faster Content Delivery**
    
    With CDN, contents are served to your user from a closer Edge server rather than the origin server thereby significantly improving latency and load time. E.g. A user from Lagos accessing a website hosted in China will have to fetch the content directly from China without CDN. But with the introduction of CDN, rather than fetching from China, nearby Edge location serves the content from a nearby Africa server making it load faster.
    
2. ### 🔐 **Improved Security**
    
    CDN always have an built-in security features like **DDoS protection**, **WAF (Web Application Firewall)**, and **bot mitigation** that prevent malicious attacks and bots by Protecting your origin server and keeps your site available during malicious attacks.
    
3. ### 💡 **Increased Reliability & Availability**
    
    CDN has the caability to reroute request to a healthyservers, cache static content and serve content even when your origin is down to ensure high uptime and smooth performance even under stress.
    
4. ### 🌍 **Global Reach & Better User Experience**
    
    We all want global relevance. We want our application to be used all over the world. With CDN, our application will work smoothly and load fast anywhere regardless how far they are from us. This is because the content they are consuming is cached in an Edge Server within their region.
    

In conclusion, user experience is paramount in this modern day product development as we are not just focusing on what the application can do alone. We also need to prioritize the experience of our users while using the application. This is where CDN and [Caching](https://kodenops.hashnode.dev/caching-deliver-content-faster-than-bolt) shine best. With this knowledge now, you can boldly tell your engineer to enable CDN on your website (all web hosting company provide this features) when deploying it to improve your customer experience significantly.