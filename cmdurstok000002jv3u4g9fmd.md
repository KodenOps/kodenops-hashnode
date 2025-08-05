---
title: "Caching: Deliver Content Faster Than Bolt"
seoTitle: "Caching: Deliver Content Faster Than Bolt"
seoDescription: "What is even Caching?
That is all what caching is all about. It’s just a process of storing frequently used data to prevent regeneration all over again"
datePublished: Sat Aug 02 2025 21:34:20 GMT+0000 (Coordinated Universal Time)
cuid: cmdurstok000002jv3u4g9fmd
slug: caching-deliver-content-faster-than-bolt
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/zBLtU0zbJcU/upload/3ff2d89ad8e1779cb1b8f60e176cfa51.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1754171907083/8d6f8045-20a9-4921-8b90-9b34f4d542ea.webp
tags: software-development, technology, backend, caching, devops, cache, system-design

---

## Introduction

You will agree with me that the world is losing their patience. Nobody want to wait for a webpage to load for 3 minutes. Maybe back in the days when our attention span hasn’t been divided across different platforms, developers and engineers can take faster page load with levity. But now that there are even tons of competitors for a particular service(s), engineers cannot toy with delivering their service to the user in the tiniest milliseconds. There are numerous ways to ensure faster delivery of content to users. This include the codelevel approach where code is stripped of redundant rendering of a task/function, lean modules in applications, clean codes, etc. What we will be focusing on will be a step further into the core engineering tooling where data are being fetched and written swiftly.

### What is even Caching?

To be honest, without speaking plenty english. Caching is just a way to temporarily store chunk of data that was previously queried such that it will be delivered faster once queried again.

Ok, that sounds like greek.

Imagine i ask you this multiplication question: 562 × 20

This might take you time to get. In fact if you might need to get a paper and pen or calculator to get the answer.

So you’ll answer me: 11,240

What happen if I ask you the same question in the next 3 minutes? You don’t need to calculate it all over again. You will tell me the answer straight from memory. Faster that before, right? What your brain literally did was to store that temporary for when that same question will be asked again. If you did not use the information within some time, it will vanish from your brain. In our lay man term, we call it **information retention.**

In Engineering, this is Caching. That is all what caching is all about. It’s just a process of storing frequently used data (cache) to prevent regeneration all over again.

**Hmmm, Why Caching Fr?**

I get you. You are wondering why? Why are we storing this information when the system can literally just search for it? Afterall, is that not what the system was built for. Maybe we can even tweak the system to be able to perform these read and write faster and more effective. You are really right to think this way. In fact, I once asked that will the system not still query the Cache for the same data. Why not just stop over-engineering and let’s just be normal with this technology thing-y.

So, here are the reason we cache

1. 🔥 **Speed Up Response Times**
    
    I cannot over-emphasize this. One terms you will keep hearing in all technology discussion is speed, faster response time, processing time, page load, request handling. Everything just have to be fast because you people are not patient like our fore-fathers. I don’t even know where all of you are rushing to.
    
    To further explain why we need caching for faster response time, let’s take this analogy.
    
    *a. You have a social media application. On this application, users can search for other users.*
    
    b. The total users on the application is +50 million.
    
    This means for you to stalk that your ex, the application will have to loop through all the +50 million users to find that big headed human being that broke your heart for you. The time complexity for this search is not always constant ( Big O of O(1)). Depending on the logic involved in the search, this can take at least 2-10 seconds to fetch the data.
    
    This mean, if you want to search for this same ex one more time, (maybe you forgot to check if he has bought any new thing in his sitting room), the database will have to be subjected to this long querying time.
    
    Ok, you forgot to check if he still have that ugly pics you snapped him at the beach, you will search again.
    
    In fact, i know you well well. You will still search him in the next two days. (Free advice, just let your ex go forever)
    
    With caching, this search is stored temporarily. So that the next time you try to stalk that Ex, it won’t loop through all the users on the app. Rather it will just give it to you from the cache storage.
    
    And to answer my question about “is it still not searching in the long run”, yes it is still searching. But this time, it’s searching from a lesser data storage which makes the response time faster.
    
2. ### 💡 **Reduce Load on Backend Systems**
    
    I’ve said this stylishly already. With cache, you don’t have to keep subjecting you backend to rigourous read and write tasks. Instead, frequently queried data are stored in a box and they are delivered immediately the user ask for it.
    
3. ### 💸 **Save Bandwidth and Costs**
    
    Reduces repeated data transfers over the network or from expensive APIs (e.g., third-party services billed per request).
    
4. ### ⏳ **Handle Offline or Slow Systems**
    
    Allows the system to serve data even when internet connectivity goes down. For instance, some website will still show your last page viewed even when you go offline. When you reload the page, instead of complaining that you are not connected, it will rather serve you from the cached data while waiting for the network connectivity.
    

### Ways To Cache

After discussing the Whys, Let’s talk about the strategies

1. **Cache Aside**
    
    **Cache Aside (a.k.a. Lazy Loading)** is a caching strategy where the application **first checks the cache** before querying the database. Here's how it works:
    
    a. The application **tries to read data from the cache**.
    
    b. If the data **exists in the cache** (cache hit), it returns it.
    
    c. If there's a **cache miss**, the application **fetches the data from the database**, **returns it**, and then **stores it in the cache** for next time.
    
    This approach ensures **data availability and resilience**, since the application can always fall back to the primary data source (e.g., a database) even if the cache is unavailable.
    
    The server writes directly to the database but for the read task, it will first query from the cache before falling back to the database.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754165577794/80615804-8025-4b66-a072-7d5a323dff14.gif align="center")
    
2. Read-Through Strategy
    
    In the this strategy, the application **never reads directly from the database**. Instead, it always queries the **cache** first.
    
    If the data is already in the cache (**cache hit**), the cached value is returned immediately.
    
    If the data is **not found** in the cache (**cache miss**), the **cache system itself** (not the application) is responsible for fetching the data from the database. Once retrieved:
    
    The cache updates its own storage with the new data.
    
    It then returns the data to the application.
    
    The application sends the response back to the client.
    
    This ensures that the cache is **always up to date** and that the application **only interacts with the cache layer**, simplifying the application code.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754166780359/0e90ec74-7540-4943-83f3-5c8b7817ca70.gif align="center")

Another concept is Wrte-Through.

> "Write-Through is conceptually similar to Read-Through, but applies to write operations instead of reads."
> 
> Write-Through is to writes what Read-Through is to reads — both ensure that the cache remains in sync by going through the cache layer.

3. **Write-Around Strategy**
    
    In the **this** strategy, the application **writes directly to the database**, **bypassing the cache**. This means the cache might be out of sync after a write, and could lead to a cache miss on subsequent reads until it's re-populated. Reads, however, are attempted from the **cache first**, and if there's a cache miss, then the DB is queried and the cache is updated
    
    > *“In write-through, the cache is updated and the database is also updated immediately (synchronously). This is different from write-around where the cache is bypassed during write.”*
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754167858509/18b12d42-ae5d-4301-b8d3-dc9393a14c03.gif align="center")
    

Write-Back/Write Behind

In this strategy, the application writes to the cache, and the cache asynchronously updates the database in the background. This reduces write latency and allows for write batching, but may risk data loss if the cache fails before syncing to the DB. Write-back introduces latency for DB updates, that’s a tradeoff for better write performance. Systems must be carefully designed to avoid loss in the event of cache crashes.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1754168329870/0fb5f552-1039-449b-a584-9044114a99e6.gif align="center")

### Other Key concept involved in caching

1. Cache invalidation: Cache will always be outdated and in turn need to be updated with fresh data. Eg. when your cache is storing total\_users = 1k. If your total\_user has increase in the database, that means the cache is outdated. The process of removing and updating stale data from the cache is called cache invalidation.
    
2. **Cache Coherency**: The process of **ensuring that the data in the cache and the data in the original data source (like the database) remain consistent and in sync** at all times. There are two major type:
    
    1. **strong consistency:** The cache is **always** in sync with the source of truth. Any update to the data in the database **immediately** updates or invalidates the cache. And in the long run, Clients **never read stale data** from the cache.
        
    2. **Eventual Consistency:** The cache is **not always immediately in sync**, but it will **eventually** reflect the correct data. When data is updated in the database, the cache is updated **later**, maybe after a time interval, event trigger, or background job. **The client might read stale data temporarily**, but the cache will **self-correct** over time. Suitable for less-critical applications like **news feeds, product listings, or analytics dashboards** where some **delay is tolerable**.
        
3. **Eviction Policy:**Due to limited capacity of the cache storage, data have to be removed from the cache to allow more space for other data to be stored. The rules that guides these removal of data is called Eviction Policy. There are quite a number of them:
    
    1. Time Based policy: This policy uses a predefined duration to decide when data should expire.
        
        * Once the TTL for a cached item expires, it is automatically removed from the cache.
            
        * Useful for data that becomes stale after a known period (e.g., weather data, access tokens).
            
    2. **Least Frequently used Policy (LFU)**: This policy removes the **least accessed** items from the cache.
        
        * It tracks how many times each item is accessed.
            
        * Data that is **rarely used** will be the first to be evicted when space is needed.
            
    3. **Least Recently Used Policy (LRU):** This policy evicts the data that has **not been used for the longest time**.
        
        * Unlike LFU, it doesn’t count access frequency but focuses on **recency**.
            
        * The idea is: if something hasn’t been used in a while, it's less likely to be used soon.
            

**In conclusion**, caching isn’t just some nerdy optimization — it’s a **vital technique** to make your apps faster, cheaper, and more scalable. Whether it's reducing DB load, speeding up requests, or handling offline states, caching plays a big role. Just remember: it’s not magic. It comes with challenges like invalidation, eviction, and data consistency. But when used wisely, caching is a superpower.

And please, stop stalking your Ex on social media. Don’t waste your cache storage on that.