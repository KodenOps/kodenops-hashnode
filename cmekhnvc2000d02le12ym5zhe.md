---
title: "Message Queues: Making Sure Nobody Fights Over Cake or Data (or Both)"
seoTitle: "MESSAGE QUEUES"
seoDescription: "A simplified article on How asynchronous handling of messages or request in a production application"
datePublished: Wed Aug 20 2025 21:32:33 GMT+0000 (Coordinated Universal Time)
cuid: cmekhnvc2000d02le12ym5zhe
slug: message-queues-making-sure-nobody-fights-over-cake-or-data-or-both
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Bl9kfAFeVQs/upload/c8b44cde592bd96de3949372dda0985e.jpeg
tags: redis, backend, devops, message-queue, rabbitmq, kafka, sre, mq, technical-writing-1

---

## Introduction

I saw something fascinating on TikTok few days ago that has been making me shout “Why” anytime the video replay in my head. Yes, I spend my free relaxation periods on TikTok these days. I stumbled on what was meant to be a **Cake Picnic In Lagos.** The idea is simple. Everyone brings their own cake to the picnic and everyone will share. You know, soA mething fun and communal.

Well, the picnic didnt end the way we all expected. And you know why?

> “Make men taste this cake na” - Burna Boy, 2025

I guess all of them took that statement by the African Giant to the extreme. Everyone wanted to have the cake all at once. Which led to a messy, unorganize and ghetto-ish scene. Some got more than enough while others just had to look and wonder where those Cake Devouring Scavengers came from.

[Watch the TikTok video here for context.](https://vt.tiktok.com/ZSAj8cD1d/)

Okay - cake, scavengers, ghetto! And Tech! What is the correlation?

One thing about technology is how we can always see the concept happening naturally in our environment.

A core concept in technology that resonate deeply with this video i saw on tiktok is Request-Response handling. Let’s take for instance, our e-commerce website where numerous users can:

* pick an item
    
* add payment method
    
* put delivery address
    
* checkout
    
* And get a mail summarizing their new order
    

How does the infrastructure handle all these request without wasting users time (high processing time)? Let’s quickly discuss this before I come back to our **Cake mess.**

## Synchronous and Asynchronous communication

To be honest, those are two freaking big words. Why the big name just to describe a simple process. Well, don’t run away because of the big name, let’s dissect the damn thing.

Let’s take for instance, you type an arithmetic operation on your calculator (Let’s say 90 × 40), by default, you as the user is expecting an immediate response from the calculator when you press the “=” button. No “come back later” for the answer. You get immediate response once you send your request. One machine is processing the request and the machine won’t handle any other request until it finish processing that particular request. Even the calculator won’t accept any request until your arithmetic answer (Response) has been provided.

That is simply **Synchronous Communication.** In simple terms, a communication where all parties are actively involve in transferring messages from one to another.

Another example is our Calls (Video or voice calls).

For Asynchronous communications, responses can be delayed and the requester is not keen on getting response immediately. Although, there is an expectation for a response. Let’s take for an instance, Whatsapp messages. When I send you a “Hi” on whatsapp, I have an expectation for a response from you. But the fact that you’ve not responded doesn’t hinder me from sending another message to another person. So, when communications are handles independently without the response being provided immediately, then we can say the communication is Asynchronous.

So using our e-commerce analogy; when you order for your favorite shoes on Amazon, you will get your order summary via email. This email is not guaranteed to come immediately. It can be few minutes late which will still be good.

**Back to our Cake Picnic.**  
Normally, the interaction between the people and the cake stand is meant to be **synchronous.**

* You walk up to the stand,
    
* ask for cake, and
    
* get it immediately.
    

Simple, right?

But what happened in that video is what I like to call **synchronous overload**: a situation where **too many requests** hit the server (or in this case, the cake stand) at the same time. The server gets overwhelmed, unsure which request to process first, and chaos ensues (just like in the video).

To solve this overload, that’s exactly what Message Queues are meant to handle.  
Imagine if, instead of letting everyone rush the cake stand at once, one person collected all the cake requests, went to the stand, got the right portions, and then served each person one by one in an orderly fashion.

That’s the essence of a **message queue**: it receives requests (messages), holds them in line, and ensures they’re processed **sequentially or based on defined rules**, preventing chaos and overload.

## What then is a Message Queue?

A message queue is a system that helps your app handle requests asynchronously by putting them in a line (or queue), usually based on first come, first served or some other rule. Instead of trying to process everything at once, it lets the system deal with each request one at a time, in an orderly and manageable way.

So, let see it as a system that handles how requests should be processed asynchronous. For clarity, let’s see it as the catering service for our cake picnic. They are in charge of handling all the request (for cakes obviously) and using any internal rule (the catering service rulebook or the organizer’s directives) they serves all the requests (cake) one by one.

Each requests can either be handled in either ordered or unordered manner. Just like I tried to explain in the previous paragraph.

* Ordered queues: This is when the request are treated in a very sequential manner. Let’s say the time of arrival (FIFO), people status (**Priority Queue), etc**. So, there is a defined order in which the consumer handles the requests. The consumer knows exactly which request to handle next, following a clear rule.
    
* Unordered Queues: In this kind of queue, requests are picked without any guaranteed order. Imagine our picnic guests writing their names on pieces of paper and dropping them into a big box. The cake server then picks names randomly, without knowing who submitted theirs first. That’s how unordered queues work. it’s all about what gets picked next, not when it arrived.
    

## Message Queues in Action: Understanding Producer-Consumer and Pub-Sub Models

As we’ve established earlier, Message queue is a system that handles user request in an asynchronous order. But how does this works?

### Producer-Consumer Models

As we all know, all requests are being generated by a source and it is being sent into the Queue. This queue acts as a buffer between the request time and response time. These requests are handled by a separate component called the **consumer**, which pulls messages from the queue and processes them..

> Producer ———&gt; MQ ——————&gt;Consumer process

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1755724530324/ac35dca7-135c-4d6a-99d2-0a7dd3f4d25c.png align="center")

* Producer: The producer sends the request. Here the producer is the source of the request coming into the queue. Our checkout API (/checkout), is a great example. On clicking the checkout button after completing payment and inserting all necessary details, it triggers the “Send request” functionality which routes the “Buy xxxx Product” request into the queue.
    
* MQ: This is the message queue that holds all the request. It is the buffer needed by the processor to remain sane and not overwhelm with requests. See it as the lobby for the request. Just as we have a lobby where job applicants waits before someone call them into the interview room. This is also the box where the picnic people drops their name.
    
* Consumer: This is the actual request processor. It picks the request one at a time and process. This is our cake server. In some case, the consumer process and then send a notification to the producer stating completion of request. While in other case, sending response is not expected by the producer.
    

Just to bring more clarity on why this is actually needed rather than just sticking to textbook concept, let’s look at real world scenerio where this is applicable.

A payment app called Konnect has a signup process that involves filling bio-data forms, upload your official IDs and other related documents, then you submit the signup for confirmation.

* **Frontend**: Once you submit, you can still login but you will have a “Pending confirmation” tag on your account and won’t be able to do critical stuff on the app
    
* Backend: All new account creation request are sent to the queue. The individual people registering as the Producer. The server then pick from this queue one by one and process the request.
    
* What is being processed: The consumer picks individual request, check the bio-data and compare them with the IDs you uploaded. It can even compare validity of core number (BVN, Passport number, Social Security Number, etc) with any official database (if available or included in the processing flow). If everything matches, it label the request “Success” and if otherwise - “Failed”. If the label is “Success”, it update your app and remove the “Pending confirmation” tag on your app and then give you full access to all functionalities. If failed, it sends notification to you that “your details are not matching - refill/reupload your document”
    
* Since your request has been treated (successful or failed), it move to the next user request in the Queue.
    

This way, you are not actively waiting for the response. Rather, you can do other things like checking the app out and doing some basic settings while the system is busy handling all the numerous signup request in the queue.

> This is a perfect example of how message queues help systems stay responsive, even when processing takes time.

### Publisher-Subscriber Model

While we’ve established how MQ handles request using the Producer - Consumer model, let’s look at how the pub-sub works. In the Pro - Con model, there is a one-way flow for the request. What ever the Producer request sent to the Queue can only be processed and used by one Consumer. Obviously, you know that there will be a model where a request will need to be processed by multiple consumers. In Pro - Con model, it is one-on-one approach. But in Publisher, it is One-to-many approach.

Following up on that our Konnect App analogy.

You’ve filled the sign-up form, upload your IDs and all necessary document.

To you as the user, you just want to be confirmed by the system.

To the App Owner/Developer, they want to perform different things with your request such as:

* Confirm your details and update your user status to “Confirmed”
    
* Enroll your number for SMS notification
    
* From your ID number, check with Bureau for credit worthiness to know if you are eligible for loan
    
* etc.
    

So, in this manner, you will need more consumers who will subscribe to that request to carry out each of these tasks. In this scenerio, we do not call them consumer, rather we refer to them as Subscribers.

* The “Confirm user” subscriber will pick the request and check customer details and confirm accuracy. Once the status is “Confirm”, it signals to other subscriber to carry out their individual tasks
    
* The “SNS” subscriber will enroll users contact details to the notification services
    
* The “Credit-Check” subscriber will also act on the same request in the queue to check if the new user is someone they can offer the loan service or not.
    
* etc
    

![Pub/Sub Messaging: What Is It? | Knowledge Base | Dashbird](https://dashbird.io/wp-content/uploads/2021/01/pub-sub-messaging.png align="left")

Now you see how one request attract different subscribers that acts independently on the request performing entirely different things on the request.

There is a little flaw which I intentionally added to explain that Pub-Sub consumption is event-driven and not state driven. That is, the subscribers doesn’t wait for the state of other subscribers. Rather they are triggered by the event of message publishing and they all work in parallel to one-another.

Can you guess what the flaw is now?

> The “Confirm user” subscriber will pick the request and check customer details and confirm accuracy. Once the status is “Confirm”, it signals to other subscriber to carry out their individual tasks

This is somehow false as the response from this subscriber doesn’t signal anything to other subscribers. They are working independently to one another. I just use that to drive understanding of the main work-flow of Pub-Sub consumption.

To fix this flaw, we can use a hybrid solution using Pro-Con and Pub-Sub.

* The flow is split into two. **Check-user** message and confirmed-user message.
    
* In Check-user message, once the user submit the sign-up form, the message is sent to the queue and a consumer will treat the request in the same manner it did for Pro-Con model.
    
* Once user is confirmed, another request is sent as a message into the queue using a **Confirmed-user** topic. Then the rest of the subscribers that subscribed to that topic can then act on the request individually
    

## How the MQs Handles Failed Requests

One thing about Site Reliability Engineering is that nothing is 100% perfect hence, [**the need to Embrace Risk**](https://sre.google/sre-book/embracing-risk/)

Consumers can encounter errors while trying to process a request. This can be due to numerous reasons.

* The message is **malformed** or contains invalid data.
    
* The consumer **crashes** or throws an error while processing.
    
* The message **exceeds retry limits** (e.g., after 3 failed attempts).
    
* The message **expires** before being processed.
    

So, how does the MQs handle this problems?

### Retry Mechanism

This is the first step for every failure. Before the system even think of sending the message to the Dead Letter Queues (DLQ), it first retry multiple times either immediately or after a delay depending on the configuration

### Dead Letter Queues (DLQ)

One way MQs handles failures is to send them to another queues. This happens after many failed retries. This Queue is mainly for all the failed requests. Instead of discarding the message and cause potential lose, the MQ send the problematic request into this "holding area" for later inspection, debugging, or reprocessing.

Think of it as the “problem cake” table at the cake picnic. The cakes that are dropped, smashed, or unidentifiable don’t get thrown away immediately. They’re put on a side table so the organizers can figure out what went wrong:

* Was the cake bad to begin with?
    
* Did someone pick it up wrong?
    
* Did the server trip while serving?
    

![FAQ: When and how to use the RabbitMQ Dead Letter Exchange - CloudAMQP](https://www.cloudamqp.com/img/blog/dead-letter-exchange.png align="left")

### **Key benefits of DLQs**

1. No silent data loss: You don’t just throw away bad messages.
    
2. Easier debugging: You can inspect failed messages to see patterns.
    
3. Keeps main queue healthy: Bad data won’t block the system.
    
4. Supports reprocessing: Once you fix the root cause, you can replay the messages from the DLQ.
    

Monitoring DLQ for unusual incremental pile-up can serves as an indicator of issue for engineers.

## Examples of Message Queues solutions

There are multiple types of MQs depending on their complexities and use cases. Some are lightweight and easy to set up (like RabbitMQ), some are fully managed cloud services that handle scaling for you (like AWS SQS or Google Pub/Sub), and some are built for extremely high throughput and massive data streams (like Kafka). They all deliver messages reliably, but each has its own strengths and trade-offs.

## **How to choose between them?**

* Need something simple & reliable to start with? → RabbitMQ or SQS
    
* Need real-time analytics, high throughput, and event replay? → Kafka
    
* Already using Redis? → Redis Streams for lightweight cases
    
* Enterprise Java environment? → ActiveMQ
    
* Serverless or cloud-first? → Google Pub/Sub or AWS SQS
    

Whether you’re serving cakes or serving API requests, chaos comes when everyone wants a piece at the same time. Message queues make sure no one gets trampled, no data gets lost, and every request gets its turn. They’re the quiet organizers behind the scenes. No shouting, no scavenging, just orderly slices for everyone. So the next time your app feels like that Lagos cake picnic, remember: you don’t need a bigger cake stand, you need a better Queue Management Integration (Literally).