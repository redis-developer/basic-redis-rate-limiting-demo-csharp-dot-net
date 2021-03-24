<div style="position: absolute; top: 0px; right: 0px;">
    <img width="200" height="200" src="https://redislabs.com/wp-content/uploads/2020/12/RedisLabs_Illustration_HomepageHero_v4.svg">
</div>
<div style="height: 150px"></div>

# Basic Redis Rate-limiting Demo .NetCore 5

Show how the redis works with .NetCore 5.

![Front example](docs/preview.png)

# How it works?

## 1. How the data is stored:
<ol>
    <li>New responses are added key-ip:<pre> SETNX your_ip:PING limit_amount
 Example: SETNX 127.0.0.1:PING 10 </pre><a href="https://redis.io/commands/setnx">
 more information</a> 
 <br> <br>
 </li>
 <li> Set a timeout on key:<pre>EXPIRE your_ip:PING timeout
Example: EXPIRE 127.0.0.1:PING 1000 </pre><a href="https://redis.io/commands/expire">
 more information</a>
 </li>
</ol>
<br/>

## 2. How the data is accessed:
<ol>
    <li>Next responses are get bucket: <pre>GET your_ip:PING
Example: GET 127.0.0.1:PING   
</pre><a href="https://redis.io/commands/get">
more information</a>
<br> <br>
</li>
    <li> Next responses are changed bucket: <pre>DECRBY your_ip:PING amount
Example: DECRBY 127.0.0.1:PING 1</pre>
<a href="https://redis.io/commands/decrby">
more information</a>  </li>
</ol>
 
---

## How to run it locally?

```
git clone https://github.com/redis-developer/basic-redis-rate-limiting-demo-csharp-dot-net.git
```

#### Write in `appsettings.Development.json` your actual access to Redis:
    "Redis": {
        "ServerUrl": "Redis server URI",
        "Password": "Password to the server"
      }

#### Run backend

``` sh
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

#### Deploy to Vercel:

<p>

<a href="https://vercel.com/new/git/external?repository-url=https%3A%2F%2Fgithub.com%2Fredis-developer%2Fbasic-redis-rate-limiting-demo-csharp-dot-net&env=REDIS_ENDPOINT_URL,REDIS_PASSWORD,PORT" target="_blank">
        <img src="https://vercel.com/button" alt="Deploy with Vercel" width="150px" height="41"/>
    </a>
</p>


#### Deploy to Google Cloud
<p>
    <a href="https://deploy.cloud.run" target="_blank">
        <img src="https://deploy.cloud.run/button.svg" alt="Run on Google Cloud" width="150px"/>
    </a>
</p>