Blue-Green Deployment with Nginx and Docker Compose

This project demonstrates a zero-downtime Blue/Green deployment strategy using pre-built Node.js container images running behind Nginx. The setup automatically fails over from the active (Blue) service to the backup (Green) instance when a failure is detected, ensuring uninterrupted service.

Overview

The stack consists of:

Nginx – Acts as a reverse proxy and load balancer, managing routing and automatic failover between Blue and Green.

Blue Node.js Service – The active service container.

Green Node.js Service – The backup service container.

Nginx automatically routes traffic to the backup (Green) instance if the primary (Blue) service fails due to timeout or a 5xx error. All health checks, retries, and upstream headers are preserved.

Features

-Blue/Green deployment model for zero downtime.

-Automatic failover handled by Nginx.

-Retries on timeout and HTTP 5xx errors.

-Tight failover timeouts (2s–4s) for quick recovery.

-Full header preservation (X-App-Pool, X-Release-Id).

-Parameterized Docker Compose configuration controlled by a .env file.

-Supports CI-based automated verification and health testing.

Endpoints:
Service	Description	URL
Nginx Gateway	Public entrypoint	http://localhost:8080
Blue App	Direct access for chaos testing	http://localhost:8081
Green App	Direct access for chaos testing	http://localhost:8082i


Application Endpoints:

-GET /version – Returns the app’s pool (blue or green) and release ID in headers.

-GET /healthz – Reports service health.

-POST /chaos/start – Simulates downtime (HTTP 500 or timeout).

-POST /chaos/stop – Stops simulated downtime.Application Endpoints

-GET /version – Returns the app’s pool (blue or green) and release ID in headers.

-GET /healthz – Reports service health.

-POST /chaos/start – Simulates downtime (HTTP 500 or timeout).

-POST /chaos/stop – Stops simulated downtime.



Environment Variables;

All configuration is managed on a .env.example file:

Variable	Description
-BLUE_IMAGE	Docker image reference for the Blue service
-GREEN_IMAGE	Docker image reference for the Green service
-ACTIVE_POOL	Defines the active pool (blue or green)
-RELEASE_ID_BLUE	Release ID returned by Blue instance
-RELEASE_ID_GREEN	Release ID returned by Green instance
-PORT	Internal port exposed by the Node.js containers (default: 8081)





Setup Instructions:

1. Clone the repository
git https://github.com/EdidiongAkpan/blue-green-deployment.git
cd blue-green-deployment/

2. Copy and configure environment variables

cp .env.example .env

Edit .env to match your desired configuration and container images.
3. Start the services

Run "docker compose up -d"

4. Verify deployment

Check that the active pool responds through Nginx:

curl -i http://localhost:8080/version

Expected headers:

X-App-Pool: blue
X-Release-Id: <release_id_blue>


Testing Failover:

1. Simulate a failure on Blue:
curl -X POST "http://localhost:8081/chaos/start?mode=error"

2. Recheck Nginx
curl -i http://localhost:8080/version

3. Stop the simulated failure:
curl -X POST "http://localhost:8081/chaos/stop"


Stopping Services:
Run Docker compose down.



THANKS FOR READING!


























