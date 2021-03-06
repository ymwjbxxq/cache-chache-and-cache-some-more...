# CACHE CHACHE and CACHE some more… #

Speaking with few nerds friends of mine, we were sharing experiences and stories. We end up maybe too much talking about cache and how many people (even us) apply cache without actually solving the problem behind it. 

Adding cache and invalidation increase complexity, and if we use it to hide problems like database queries, API calls that are too slow at some point, they will bite us back. 

A cache should be used to decrease cost and increase performance, not to solve them, and so before applying cache, maybe we should answer:

* Will cache reduce cost?
* Will it increase scalability and performance?
* How stale data will affect the application?
* Do we need cache or rewrite part of the application to make it more scalable and performant?

In AWS serverless world, we have multiple levels where we can cache:

![picture](https://github.com/ymwjbxxq/cache-chache-and-cache-some-more.../blob/master/cache.png)

The first place is CloudFront, and its purpose is to reduce the number of requests to the origin.

Cloufront can cache content based on:

* [QueryString]( https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/QueryStringParameters.html)
* [Request Header](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/header-caching.html)
* [Cookies](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Cookies.html)

The second place is [API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html). You can reduce the number of calls and improve the latency of requests. The default TTL value is 300 seconds.

Third place is the [Lambda]( https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html) where we need to take advantage of execution context reuse

The final stage is [DynamoDB DAX](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DAX.concepts.html), and it should be transparent for your code, but the Read Operations can return stale data and so TTL needs to be configured.


### What if I do not use serverless services? ###

![picture](https://github.com/ymwjbxxq/cache-chache-and-cache-some-more.../blob/master/cache2.png)

CloudFront also works together with ALB and EC2 and is common to use [ElastiCache](https://aws.amazon.com/elasticache/), and you need to be aware of [caching strategies](https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/Strategies.html) to use in your application.
Because ElastiCache comes in [two flavours](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/SelectEngine.html), you need to make sure to pick the right one. 

Managing the cache is extra complexity, and so we should not jump straight on it. If we are going on this road:

* We need always apply TTL even for few seconds in case you have unpredictable and rapidly changing data and the cache is not only at one level but is a multi-level matter
* Warm-up the cache, considering peak hours to ensure the CDN is given enough time to populate the cache
* Test your application for [cluster resizing](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/best-practices-online-resharding.html)
