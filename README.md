<div style="position: absolute; top: 0px; right: 0px;">
    <img width="200" height="200" src="https://redislabs.com/wp-content/uploads/2020/12/RedisLabs_Illustration_HomepageHero_v4.svg">
</div>
<div style="height: 150px"></div>

# A Rate Limting Demo app in .NetCore 5 using Redis

Show how the redis works with .NetCore 5.

# Overview video

Here's a short video that explains the project and how it uses Redis:

[![Watch the video on YouTube](https://raw.githubusercontent.com/redis-developer/basic-redis-rate-limiting-demo-csharp-dot-net/master/docs/YTThumbnail.png)](https://www.youtube.com/watch?v=_mFWjk7ONa8)

# How it works?

## 1. How the data is stored:

- New responses are added key-ip: `SETNX your_ip:PING limit_amount`

  - E.g `SETNX 127.0.0.1:PING 10`
    <a href="https://redis.io/commands/setnx">more information</a>

- Set a timeout on key: `EXPIRE your_ip:PING timeout`
  - E.g `EXPIRE 127.0.0.1:PING 1000`
    <a href="https://redis.io/commands/expire">more information</a>

## 2. How the data is accessed:

- Next responses are get bucket: `GET your_ip:PING`

  - E.g `GET 127.0.0.1:PING`
    <a href="https://redis.io/commands/get">more information</a>

- Next responses are changed bucket: `DECRBY your_ip:PING amount`

  - E.g `DECRBY 127.0.0.1:PING 1`
    <a href="https://redis.io/commands/decrby">more information</a>

##### Code used for configuring rate limiting

```C#
using AspNetCoreRateLimit;
// ...

services.AddStackExchangeRedisCache(options =>
{
    options.InstanceName = "master:";
    options.ConfigurationOptions = ConfigurationOptions.Parse(redisConnectionUrl);
});

services.Configure<IpRateLimitOptions>
(Configuration.GetSection("IpRateLimit"));
services.AddSingleton<IIpPolicyStore, DistributedCacheIpPolicyStore>();
services.AddSingleton<IRateLimitCounterStore, DistributedCacheRateLimitCounterStore>();
services.AddSingleton<IRateLimitConfiguration,RateLimitConfiguration>();

//...
app.UseIpRateLimiting();
```

---

## How to run it locally?

```
git clone https://github.com/redis-developer/basic-redis-rate-limiting-demo-csharp-dot-net.git
```

#### Write in environment variable or Dockerfile actual connection to Redis:

```
   PORT = "API port"
   REDIS_ENDPOINT_URL = "Redis server URI"
   REDIS_PASSWORD = "Password to the server"
```

#### Run backend

```sh
dotnet run
```

Static сontent runs automatically with the backend part.

## Try it out

#### Deploy to Heroku

<p>
    <a href="https://heroku.com/deploy" target="_blank">
        <img src="https://www.herokucdn.com/deploy/button.svg" alt="Deploy to Heorku" />
    </a>
</p>

#### Deploy to Google Cloud

<p>
    <a href="https://deploy.cloud.run" target="_blank">
        <img src="https://deploy.cloud.run/button.svg" alt="Run on Google Cloud" width="150px"/>
    </a>
</p>
