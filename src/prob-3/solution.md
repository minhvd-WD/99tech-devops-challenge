# Problem 3: Diagnose Me Doctor

## Troubleshooting step
### Step 1: Immediate Mitigation (Handling the Overload)  
Redirect traffic to a standby VM or backup server.
- Action: Update the load balancer (if applicable) to route traffic to an alternative server.
- Goal: Reduce incoming traffic on the affected VM to prevent downtime.
  
### Step 2: Inspect current state the overloaded VM
Identify the cause of high memory usage before taking drastic action
- Check top processes consuming memory: `ps aux --sort=-%mem | head -10`
- List active NGINX connections: `netstat -anp | grep :80 | wc -l`
- Monitor real-time memory usage: `free -h`

### Step 3: Reduce the current load on the overloaded VM  
Attempt to relieve system pressure before restarting to minimize downtime.
- Option 1: Restart nginx
- Option 2: Restart VM

### Step 4: Deeply inspect nginx log
Find the real reason behind high memory usage.
- Analyze the number of requests (detect DDoS or peak load) to determine if the traffic spike was legitimate or an attack.
- Check for excessive requests from specific IPs to detect IP addresses that might be flooding the server.
- Look for repeated 500 errors in error log (backend failures that could be contributing to resource exhaustion).

## Possible causes/scenario
### 1: Peak Load (High Legitimate Traffic)
A. Impacts  
-	Increased Memory & CPU Usage: More active connections cause higher resource consumption.
- Delayed Responses: NGINX struggles to handle concurrent requests, leading to slow load times.
- Potential Server Crashes: If resource exhaustion occurs, the system may become unresponsive.
- Dropped Requests (504 Gateway Timeout): If upstream services (like the database) are overwhelmed, requests may fail.
- High Network Load: A surge in traffic can saturate bandwidth, affecting overall performance.

B. Recovery solution
- Optimize Load Handling
  - Enable Rate Limiting to Control Traffic Surges
  - Implement Connection Limits
- Scale Infrastructure
  - Increase Server Resources (Temporary Fix)
  - Horizontal Scaling (Permanent Solution)

### 2: Backend Failure (Repeated 5xx Errors)
A. Impacts  
-	HTTP 500 Errors: Users see “Internal Server Error” messages.
-	High Response Time (Latency Spikes): NGINX keeps retrying failed backend requests.
- Potential Memory Leaks: Faulty backend services may cause unreleased memory accumulation.
- Service Downtime: If critical backend services fail, NGINX cannot serve requests properly.

B. Recovery solution
- Debugging the Backend Failure
  - Check Backend Logs for Errors
  - Test API/Backend Response
- Fix the Underlying Issue
  - Database Connection Issues
  - Application-Level Fixes: 
    - Memory Leak Fix: Ensure proper garbage collection in backend code
    - Database Optimization: Reduce expensive queries or add caching.
- Implement Failover & Resilience