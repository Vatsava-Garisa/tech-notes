
# **What** 
Source IP is the IP address of the system that initiated the request or event.
- Source IP - The machine that sent the request.
- Destination IP - The machine that received the request.

```
Client & Server:
----------------
Client (192.168.1.45)  ----->  API Server (10.0.0.12)
         Source IP                Destination IP

Internal Systems:
-----------------
Service A (10.10.2.5)  ----->  Service B (10.10.2.8)
         Source IP                Destination IP
```

# **Why** 
Even background workers, queues, or internal services often log `sourceIP` because it is extremely useful for observability, debugging, and security auditing.

## 1. Identify who triggered the event 
- Tells us from which user and from which network triggered the action.
Example:
```
2026-03-07 10:15:22
Action: CreateTrip
UserId: 10231
SourceIP: 49.37.214.18
```

## 2. Debug distributed systems 
In microservices architecture:
```
Browser → API Gateway → Auth Service → Order Service → Queue → Worker
```
- If something fails, `sourceIP` helps trace the request origin.
Example:
```
Worker log:
jobId=123
sourceIP=10.0.1.12
```
Now you know service produced the job.

## 3. Detect malicious activity 
If thousands of requests come from the same IP:
```
Login failed
sourceIP: 103.21.244.15
Login failed
sourceIP: 103.21.244.15
Login failed
sourceIP: 103.21.244.15
Login failed
sourceIP: 103.21.244.15
```
- You can detect
	- Brute force attacks
	- Bots
	- Suspicious traffic
- Then block the IP using
	- Firewall
	- API gateway
	- Rate limiter

## 4. Compliance and auditing 
Many enterprise systems must keep:
- Who performed an action
- From where
Example:
```
User: admin
Action: Delete Payroll Records
SourceIP: 49.37.214.18
Time: 11:30:02
```
This is critical for:
- Finance systems
- HR systems
- Healthcare systems
- Enterprise security audits

## 5. Investigate production issues 
Example Case: 
- Job failed in worker
Example Log:
```
jobId: 7843
sourceIP: 10.4.2.15
service: report-service
```
Now we know:
- `report-service` triggered the job.
Without `sourceIP`, you may not know which system initiated it.

# **Example Structure** 
```
{
	"timestamp": "2026-03-07T12:10:21Z",
	"service": "trip-worker",
	"action": "createTrip",
	"userId": 10231,
	"sourceIP": "49.37.214.18",
	"jobId": "TRIP_98231",
	"status": "success"
}
```

# **How** 
## 1. Express.js 
- `req.ip` returns the client IP.
```
app.get("/api", (req, res) => {
	const ip = req.ip;
	console.log(ip);
})
```

## 2. When behind a Load Balancer / Reverse Proxy 
Most production deployments use:
- NGINX
- AWS Application Load Balancer
- Cloudflare
In that case the real client IP is inside headers.
- **Enable Trust proxy** 
```
app.set('trust proxy', true);
```
Then:
```
const ip = req.ip;
```
Express will correctly read from: `X-Forwarded-For`

## 3. Safest approach 
Many enterprise systems explicitly check headers.
```
function getClientIP(req) {
	return (
		req.headers['x-forwarded-for']?.split(',')[0] || 
		req.socket?.remoteAddress || 
		null
	);
}
```
Usage:
```
const ip = getClientIP(req);
console.log(ip);
```
This handles:
1. Load balancers
2. Proxies
3. Direct connections

## 4. Raw Node.js HTTP server 
```
import http from 'http';

const server = http.createServer((req, res) => {
	const ip = req.socket.remoteAddress;
	console.log(ip);
	
	res.end('ok');
});

server.listen(3000);
```

## 5. Logging IP in middleware 
In production systems, create logging middleware.
```
app.use((req, res, next) => {
	const ip = req.headers['x-forwrded-for']?.split(',')[0] || req.socket.remoteAddress;
	
	console.log({
		method: req.method,
		url: req.url,
		sourceIP: ip
	});
	
	next();
});
```
Example log:
```
{
	"method": "POST",
	"url": "/api/trips",
	"sourceIP": "49.37.214.18"
}
```

## 6. When your Node service is called by another service 
Then the IP will be something like:
```
10.x.x.x
172.x.x.x
192.168.x.x
```
These are private network IPs inside your infrastructure.
Example:
```
API Gateway → Auth Service → Worker
```
Worker logs may show:
```
sourceIP: 10.0.4.21
```

## 7. If you want the actual user's public IP 
Use:
```
req.headers['x-forwarded-for']
```
Example value:
```
49.37.214.18, 10.0.0.4
```
First IP = real client

## 8. Production-grade utility 
Recommended:
```
export function getSourceIP(req) {
	const forwarded = req.headers['x-forwarded-for'];
	
	if (forwarded) {
		return forwarded.split(',')[0].trim();
	}
	
	return req.socket?.remoteAddress || null;
}
```

## 9. Manual adding 
Most add the headers by default.
- NGINX
- Kong
- AWS API Gateway
- AWS Application Load Balancer
- Cloudflare
- Envoy
So normally:
```
Gateway → automatically injects headers
Microservice → reads headers
```

You only manually add them if:
- Service A calls Service B
- And you want to propagate the original client IP
Example:
```
Client → CDN → Gateway → Service A → Service B

X-Forwarded-For:
49.37.214.18, 172.68.14.21, 10.0.2.5
```
Service A forward the header:
```
axios.get("http://service-b/api", {
	headers: {
		"X-Forwarded-For": req,headers["x-forwarded-for"]
	}
});
```
This preserves the original client IP across services.

# **NOTE** 
Often the real client IP is not the same as `req.ip` because of:
- Load balancers
- API gateways
- Proxies
So production systems usually use:
```
X-Forwarded-For header
```
Example: First IP = **real client** 
```
X-Forwarded-For: 49.37.214.18, 10.0.0.4
```

## Security Note 
- `X-Forwarded-For` can be spoofed because it is just an HTTP header.
```
X-Forwarded-For: 1.2.3.4
```
- A malicious client could send this directly.
- Therefore in production systems you should only trust it when it comes through a trusted proxy.
```
Express.js
----------
app.set('trust proxy', true);
```
- It tells Express.js: "Only trust forwarded headers if the request came through a configured proxy."

