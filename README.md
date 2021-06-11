<div style="position: absolute; top: 0px; right: 0px;">
    <img width="200" height="200" src="https://redislabs.com/wp-content/uploads/2020/12/RedisLabs_Illustration_HomepageHero_v4.svg">
</div>
<div style="height: 150px"></div>

# Rate Limiting app in .NET using Redis

This demo shows how how to use Redis in .NET 5 to implement IP Rate limiting to prevent excessive calls to your app from a single client.

## Technical Stack

- Frontend: ASP.NET Core MVC
- Backend: ASP.NET Core MVC / Redis

## How it works?

### 1. How the data is stored:

- New responses are added key-ip: `SETNX your_ip:PING limit_amount`

  - E.g `SETNX 127.0.0.1:PING 10`
    <a href="https://redis.io/commands/setnx">more information</a>

- Set a timeout on key: `EXPIRE your_ip:PING timeout`
  - E.g `EXPIRE 127.0.0.1:PING 1000`
    <a href="https://redis.io/commands/expire">more information</a>

### 2. How the data is accessed:

- Next responses are get bucket: `GET your_ip:PING`

  - E.g `GET 127.0.0.1:PING`
    <a href="https://redis.io/commands/get">more information</a>

- Next responses are changed bucket: `DECRBY your_ip:PING amount`

  - E.g `DECRBY 127.0.0.1:PING 1`
    <a href="https://redis.io/commands/decrby">more information</a>

#### Code used for configuring rate limiting

When configuring constructing our app's middleware in Startup.cs, we initialize the cache client and inject it into our services. We then pull from the configuration the `IpRateLimit` section, and use that as the configuration for `IpRateLimitOptions`

```C#
using AspNetCoreRateLimit;
// ...

services.AddStackExchangeRedisCache(options =>
{
    options.ConfigurationOptions = ConfigurationOptions.Parse(redisConnectionUrl);
});

services.Configure<IpRateLimitOptions>(Configuration.GetSection("IpRateLimit"));
services.AddSingleton<IIpPolicyStore, DistributedCacheIpPolicyStore>();
services.AddSingleton<IRateLimitCounterStore, DistributedCacheRateLimitCounterStore>();
services.AddSingleton<IRateLimitConfiguration,RateLimitConfiguration>();

//...
app.UseIpRateLimiting();
```

The `IpRateLimit` section is from the `appsettings.json` file:

```json
"IpRateLimit": {
  "EnableEndpointRateLimiting": true,
  "StackBlockedRequests": false,
  "RealIPHeader": "X-Real-IP",
  "ClientIdHeader": "X-ClientId",
  "HttpStatusCode": 429,
  "GeneralRules": [
    {
      "Endpoint": "*:/api/*",
      "Period": "10s",
      "Limit": 10
    }
  ]
}
```

This section dictates the period the path which limitations will be applied to, `Endpoint`, the period over which restrictions are considered, `Period`, and the Limit for the number of requests permitted in that period `Limit`

---

## How to run it locally?

```
git clone https://github.com/redis-developer/basic-redis-rate-limiting-demo-csharp-dot-net.git
```

#### Write in environment variable or Dockerfile actual connection to Redis:

```
   REDIS_ENDPOINT_URL = "Redis server URI:PORT"
   REDIS_PASSWORD = "Password to the server"
```

#### Run backend

```sh
dotnet run
```

Static content runs automatically with the backend part.

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
