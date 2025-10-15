# DummyRequestCounter
 
##  Overview
This project is a simple multi-container application using **Flask** (Python) and **Redis**.  
Every time you open the main page, the app increments and displays a counter stored in Redis.
 
---
 
##  Project Structure
```
DummyRequestCounter/
├── app/
│   └── app.py
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
├── .env
└── README.md
```
 
---
 
##  How to Run the Application
 
###  Build and Start Containers
```bash
docker compose up --build
```
 
###  Access the Application
Open your browser and visit:  
 **http://127.0.0.1:8000**
 
Each refresh increments the counter stored in Redis.
 
---
 
##  Persistent Storage (Volume)
 
To make sure the Redis data is not lost when containers stop or restart, a **named volume** was added in `docker-compose.yml`.
 
```yaml
volumes:
  redis_data:
```
 
Inside the Redis service:
```yaml
volumes:
  - redis_data:/data
command: ["redis-server", "--appendonly", "yes"]
```
 
This allows Redis to store its data on disk and automatically reload it when the container restarts.
 
You can verify the volume:
```bash
docker volume ls
```
 
---
 
##  Custom Network
 
A custom bridge network called **dummy_network** was created to handle communication between the containers.
 
```yaml
networks:
  dummy_network:
    driver: bridge
```
 
Both services use this network:
```yaml
networks:
  - dummy_network
```
 
You can verify the network and see which containers are attached to it:
```bash
docker network ls
docker network inspect dummyrequestcounter_dummy_network
```
 
---
 
##  Verify Everything Works
 
1. Run the containers:
   ```bash
   docker compose up -d --build
   ```
2. Visit **http://127.0.0.1:8000**
3. Check Redis data:
   ```bash
   docker compose exec redis redis-cli GET hits
   ```
4. Stop and restart containers — the counter should persist:
   ```bash
   docker compose down
   docker compose up -d
   ```
 
---
 
##  Troubleshooting and Debugging
 
To view container logs:
```bash
docker compose logs
```
 
To run commands inside a container:
```bash
docker compose exec api sh
docker compose exec redis redis-cli
```
 
---
 
##  Cleanup
 
To stop and remove containers, networks, and volumes:
```bash
docker compose down -v
```
 
---
 
##  Summary
- The Flask API and Redis services are containerized and orchestrated with Docker Compose.  
- Redis data is now **persistent** thanks to the named volume `redis_data`.  
- A **custom bridge network (`dummy_network`)** enables secure communication between both containers.  
- The application runs correctly on **http://127.0.0.1:8000** and keeps track of visits even after restarts.  
- Docker commands and configurations are documented for easy reuse and debugging.
 
 ### CI/CD test run — trigger pipeline
