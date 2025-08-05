---
title: "Monitoring Like A Pro"
seoTitle: "Observability & Monitoring of application performance"
seoDescription: "A Beginner’s Guide Into Application And Infrastructure Monitoring."
datePublished: Sat Aug 02 2025 16:32:37 GMT+0000 (Coordinated Universal Time)
cuid: cmduh0tru000f02jp02xodsy5
slug: monitoring-like-a-pro
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1754152670142/8489d68e-04b5-42a7-9038-c2e9ba91eb30.webp
tags: data-science, monitoring, devops, grafana, observability, performance-monitoring

---

# **The Introduction**

Having a great application with numerous users is the dream of all businesses. You know, you create a solution via website/application medium and you have real users genuinely consuming the solution daily. This is super cool.

Creating a solution is great. Maintaining the solution to ensure continuous delivery of the great service is where the real work is. At every point, you’ll want to keep your users happy and satisfied. This could be in terms of:

1. How fast the page loads for them
    
2. Availability of the services they consummate on your platform
    
3. Compatibility to their major devices
    
4. Quick resolution of the issues faced
    
5. Etc.
    

There should be a way to have an insight on these key indicators of customer’s satisfaction.

How do you know your application is lagging and properly rectify the load distribution? How do you know that users are able to access the platform? Is your system processing only 10% of the transactions while the remaining 90% is failing?

These and many more are the reason you need to have a keen eyes on your application.

Among other benefits, Monitoring your application:

* Ensures business continuity as you have eyes on your application and could quickly resolve before it goes beyond your capability
    
* Maintains customer satisfaction
    
* Enhances security
    
* Supports scalability
    
* Reduce the time to detect and resolves issues
    

# **What Do you Even monitor?**

Zoom image will be displayed

![](https://miro.medium.com/v2/resize:fit:875/0*PTvwh4V7Dxenk7cJ align="left")

Photo by [Buddha Elemental 3D](https://unsplash.com/@buddhaelemental3d?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

As it has been establish already, there is a need to monitor our application to continually provide the satisfaction your customers deserve. But what exactly do you monitor and How do you know what exactly to monitor?

Monitoring is quite simple. The logic is to create observability for a particular determinant of availability and usability across the entire application-scape. depending on the kind of application in question, the metrics to determine if that component of the application is accessible to users or not differs.

For a websites like E-commerce, the determinant could be latency of the page load, the availability of the payment API end-point, the latency and availability of the authentication service (How fast and available users can login to the website), etc.

For a banking app whose components are built as micro-service, this might be the monitoring of each micro-services for Availability, and maybe how fast they process transactions.

The whole idea is to figure out what could make the user experience terrible if it goes wrong. And apart from user’s experience, you also want to monitor the key determinant of your business logic.

For example:

You want to monitor if users can access your platform seamlessly and how smooth their journey is across the platform — User Experience

You also want to monitor if your core business logic (what exactly you are selling to customer) is working fine. — Business/Transaction monitoring

# **What key metrics do you now monitor?**

The core of your focus for monitoring will span across these components

1. Latency: latency is an expression of how much time it takes for a data packet to travel from one designated point to another. This monitor how fast or slow your component is in processing the business logic. For instance, how fast the page loads or how fast the API is processing and returning a response.
    
2. Errors: This monitor the errors being experienced across the application. This gives insight into how accessible your application is. Also, there will be a need to differentiate between valid and not-valid errors. Valid errors are those induced by your system while Not-Valid errors are the errors being induced by the customers and not dependent on your system. For instance, a customer trying to login with wrong username and password. Obviously, this will throw error. But it is not because your system is malfunctioning but because your customer is trying to enter the app with wrong details
    
3. Saturation: Saturation can be understood as the load on your network and server resources. This monitors the number of transactions your server is processing per time. Obviously, this points you to know if your server is being over-utilized and if there is a need to scale in or out. From the last statement, you can pin-point that saturation monitoring could give you a wide insight into the capacity utilization of the infrastructure which in turn serves as a trigger for proper capacity planning to avoid surprises. Key metrics for saturation might be in percentage (% CPU utilization), MB/GB (Memory utilization in bytes), network bandwidth, etc.
    
4. Availability: Woops! You know, at times I feel availability is the inverse of Errors. (Hear me out). Availability refers to the percentage of time a service is functioning and accessible to users as intended. This loosely mean when the service is not available, then we get errors (Valid Errors). Even though this is to be taken loosely, it is to be noted that they are not always a perfect inverse of each other. While availability picks the entire components as a whole, Errors focuses on a particular process within the component. For instance, Let’s pick the bank’s mobile app for an example. While availability might pick the entire micro-service processing a transfer action (is the transfer service up or not), errors will focus on a particular the action itself (is transfer failing or not — how many is failing per minute).
    
5. Traffic: This is the amount of footfall across your infrastructure. How many request is your server receiving and processing per time? common unit for traffic might include the following Requests per second (RPS), queries per second (QPS), data transfer rate (e.g., Mbps), transactions per second, etc.
    

# **How Then Do You Monitor**

Monitoring could be done using various tools depending on how much you are willing to pay and the depth of monitoring you are willing to go to. These tools spans from simple reporting platforms like PowerBi to more advance tools like Elastic Stack or Dynatrace. The complexity and the learning curve differs from one tool to another.

But regardless of the tools, the underlying mechanism is the same.

1. Understand the metric for availability to be monitored: I might have used “availability” loosely here and might be confusing especially if you think I mean you should only monitor availability out of all the key metrics i listed earlier. No, this is not what I am saying. Let’s pick a bank’s application for an instance again (don’t mind me, this is what my brain keeps directing me to). What are the metrics that customer might judge it on to ascertain if it is useful to them or not? Also, based on our 0 and 1 knowledge of our technological infrastructure, what are those criteria we can use to judge if we are doing well out there in terms of rendering our service or not. Finally, we can also look at the experience of users across each component of the app to know if our UX is actually working as our jagaban UI/UX expert has convinced us. All these pointers points us to one thing. WHAT SHOULD WE MONITOR
    
2. Figure out the Data Source: Now we know what we want to monitor. Oh! we need to monitor how fast our APIs are processing. We need to know how many error the APIs are generating per day to know if the API is healthy or not. Wait, we need to monitor the server capacity and capability. FINE DUDE! But where the heck are you going to get the data to monitor? Where is it residing? Now, in my own opinion, this is where the bulk of the work lies. Like one of my boss will say whenever he is trying to charge us to go haywire with our use of information. He would say “data is a goldmine” what you do with it is limitless. Now, you need to find where this honeypot is located so as to make magic with it. There are various source you can utilize as our data source. Each with its own super power and Kryptonites. And the ability to understand the trade-offs for each of these datasource makes you the Thanos of monitoring. Well, in terms of having all the infinity stones at once. Technically, you can wipe off the entire source if you like also. But I trust you won’t want to do that.
    

a. **Database**: To me, this is like the easiest source of data. Most of the transactions being done on the platform will be sent to the database. From this DB, you can write a simple (or not so simple) queries to pick the metrics you need. Send the data to aggregator and then visualize it per time.

> ***Limitation****: What about transactions that didn’t hit the database? What about the errors that occurs at the UI part of the application or web. They wont be stored on the DB.*

b. **The Server**: Well this is technically a datasource especially if you want to monitor the capacity of the server itself or if you want to monitor the logs being generated and stored on it.

> ***Limitations****: Business data might not be stored directly on the server itself. To me, it is majorly a source for capacity and log management.*

c. **Application Itself:** Applications can be integrated with s sniffer that will be spying on what users are doing on the application and then send a log of the process to the server (as noted in bullet b)

d. **APIs**: These can also serves as a point to get data from. Some critical data that can be gotten from API Cclusters might include but not limited to: HTTP requests/responses and API health checks.

3\. Figure out the pipeline to use in fetching and storing this data: This is also a crucial part of your entire monitoring process. Let me step back a bit and give an oversight on your data lifecycle. Technically, the data you are picking from the source are somewhat scattered, unorganized, unstructured, etc. This data needs to pass through a pipeline that ensure that it is structured and indexed (That is, it is properly labelled within a timeframe to be easily fetch-able). Then these indexed data are stored in a bucket from which they can be picked at intervals to be displayed on the dashboard.

## **An example to ensure more understanding:**

Let’s assume we want to monitor transactions being done on our mobile app. The information was generated from the applications and stored on the Database. So, we need to pick this data and visualize. Sounds simple.

* You need to write a SQL script to fetch the right information
    
* You need a tool that can be running this script at interval and the result of each query is sent to another tool that will index it
    
* You might also need another tool that will filter/manipulate the result. For instance, if result from the query is a long string that contains keywords you needs (e.g. ERROR, SUCCESS,WARNING, PROCESSED,etc), you can further use another tool (GROK, DISSECT, etc) to further filter the data
    
* Then you send this data to the bucket that’ll keep the processed data.
    
* Connect the visualization tool to the bucket and start visualizing
    

For the hands-on that I’ll be working with in the coming series, I will be using the following pipeline.

Data source (DB, Server, Application) — — → NiFi&Kafka (fetch the data) — — — → Logstash (index the data) — — — → Elasticsearch (store the data) — — → Kibana & Grafana (visualize the data)

> *The idea is the same no matter the tools you choose to use. It’s always the same flow (maybe with slight variation though)*

4\. Figure out the right way to visualize these data for proper understanding and interpretation: This is just basic understanding of data visualizing. Bar chart, line graph, guage, etc. The idea is to use the right form of chart to display your information in a way you could understand easily and be able to make decisive actions without plenty thoughts.

5\. Configure proper alerting pipeline and visual Ques: No long talk here. Configure the connectors to either email engine or any other ones from where you can be receiving alerts whenever a threshold is about to be reached/ reached already. It’s as simple as that. And there you have your fully configured monitoring for your application.

This is handful, I know. But this is the concept that all monitoring processes follows. Once you understand this, you can get started on any task involving monitoring. All you just need is to learn the tool and configurations.

Learnt something today? You can share what part of this article you finds more insightful.