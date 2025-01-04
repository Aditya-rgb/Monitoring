# Application Setup and Configuration with Log Aggregation

## Introduction
This document outlines the setup and configuration process for a MERN stack application integrated with Grafana, Loki, and Promtail for log aggregation and visualization. It provides detailed steps for setting up the front-end, back-end, log collection, and dashboard creation.

## Features
- MERN stack application with customizable ports for front-end and back-end.
- Integrated Grafana dashboard for monitoring and visualizing logs.
- Log aggregation using Loki and Promtail for efficient storage and querying.
- Scalable and configurable architecture for real-time log insights.

## Prerequisites
Before proceeding, ensure the following prerequisites are met:
- A server/VM (e.g., AWS EC2) with Ubuntu or any Linux-based OS.
- Node.js and npm installed.
- Grafana installed.
- Loki and Promtail installed.
- Basic knowledge of system ports and application configurations.

## Installation

## 1. Clone the Repository

```bash
git clone https://github.com/your-repository/application.git
cd application
```

### Connecting the Backend Code to MongoDB and Hosting the Application

### Steps to Connect the Backend to MongoDB

1. **Navigate to the Backend Directory**:
   After cloning the Travel Memory application repository on your EC2 instance, navigate to the backend directory:
   ```bash
   cd /home/ubuntu/TravelMemory/
   cd Backend
   sudo touch .env # Created an environment file to store sensitive information such as the MongoDB URI and port number:
   nano .env
   #Pasted the below lines inside .env file
   #MONGO_URI='mongodb+srv://adityavakharia:Alphavak98@cluster0.7powl.mongodb.net/Travel-Memory-DB'
   #PORT=3001
   sudo apt install npm # Installed NPM and Dependencies and installed the necessary Node.js dependencies:
   npm install
   node index.js # Start the backend server
   ```
   Output on the EC2 inside : Server started at http://localhost:3001
   Now, went back to AWS console, when to my instance, went to "Security", went inside my security, clicked on edit inbound rules and added the below port details:
   ```bash
   custom-TCP TCP 3001 Custom 0.0.0.0/0
   ```
     
   Copied the public IP Address of the EC2 instance, went to the browser and pasted the below url :
   ```bash
   http://34.223.88.56:3001/
   ```
 ![Alt Text](images/MongoDB-Snippet.JPG)


## 2. Establishing the Connection Between the Frontend and Backend Code Bases

1. Opened a new terminal on the same EC2 instance:
    ```bash
    cd /home/ubuntu/TravelMemory/
    cd Frontend
    cd src
    nano url.js
    ```

2. Added the following lines to `url.js`:
    ```javascript
    export const baseUrl = process.env.REACT_APP_BACKEND_URL || "http://34.223.88.56:3001/";
    ```
3. The public IP address of the EC2 instance where the backend application is hosted was pasted in the `url.js` file.
   
4. Started the frontend application:
    ```bash
    cd ..
    npm start
    ```
    

5. Updated Security Group Rules:
    - Went to the AWS console, navigated to the instance, then to "Security".
    - Edited the inbound rules and added a new rule for port 3000 and 3001 too.

6. Verified the setup by pasting the following IP address into the browser:
    ```plaintext
    http://34.223.88.56:3000/";
    ```

## 3. Install and Configure Grafana

### Start Grafana
Start and enable the Grafana service using the following commands:

```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```
### Access Grafana
Grafana is accessible at:  
```bash
http://<server-ip>:3000
```

**Default credentials:**  
- **Username:** admin  
- **Password:** admin

### Change Grafana Port (Optional)
To change the port (e.g., to 5000):  

1. Open the `grafana.ini` configuration file:  
   ```bash
   sudo nano /etc/grafana/grafana.ini
   ```

2. Update the [server] section:
   ```bash
   [server]
   http_port = 5000
   ```

3. Restart Grafana:
   ```bash
   sudo systemctl restart grafana-server
   ```

## 4. Install and Configure Loki

### Download and Set Up Loki
Run the following commands to download and set up Loki:

```bash
wget https://github.com/grafana/loki/releases/download/v2.8.0/loki-linux-amd64.zip
unzip loki-linux-amd64.zip
chmod +x loki-linux-amd64
```

### Create Loki Configuration
Create a loki-config.yaml file with the following content:
```yaml
auth_enabled: false

server:
  http_listen_port: 3100

storage_config:
  boltdb_shipper:
    active_index_directory: /tmp/loki/index
    cache_location: /tmp/loki/cache
  filesystem:
    directory: /tmp/loki/chunks

scrape_configs:
  - job_name: 'mern-logs'
    static_configs:
      - targets: ['localhost']
        labels:
          job: 'mern-logs'
          __path__: /path/to/mern/logs/*.log

```





