# 🌿 Automated Greenhouse Management System (AGMS)

A cloud-native, microservice-based platform for modern agriculture. This system automates greenhouse management by connecting to external IoT sensors, processing real-time telemetry, and triggering actions to maintain ideal growing conditions.

![System Architecture](https://via.placeholder.com/800x400.png?text=AGMS+Microservices+Architecture) 
*(Note: Replace the above URL with an actual architecture diagram if you have one)*

## 📁 Project Structure

The project is divided into several independent microservices. Here is the recommended folder structure:

```text
AGMS-Project/
├── infrastructure/
│   ├── config-server/           # Port: 8888 | Centralized Spring Cloud Configuration
│   ├── eureka-discovery/        # Port: 8761 | Netflix Eureka Service Registry
│   └── api-gateway/             # Port: 8080 | Route handling & JWT Security layer
├── domain/
│   ├── zone-service/            # Port: 8081 | Zone limits & Device Management
│   ├── sensor-service/          # Port: 8082 | IoT Data Fetcher (Scheduled Task)
│   ├── automation-service/      # Port: 8083 | Rule Engine (Decision Making)
│   └── crop-service/            # Port: 8084 | Plant lifecycle management
├── docs/                        
│   └── eureka-dashboard.png     # Screenshots (e.g. Services status)
├── AGMS_Postman_Collection.json # Essential API endpoints for testing
└── README.md
```

## 🏗️ Microservices Overview

### 1. Infrastructure Services
- **Config Server:** Manages environment properties externally from a central location.
- **Eureka Server:** Service registry so microservices can find each other dynamically without hardcoded IPs. 
- **API Gateway:** Intercepts external traffic, checks JWT tokens, and routes requests to the correct backend service.

### 2. Domain Services
- **Zone Management (`8081`):** Defines greenhouse zones (e.g., "Tomato Zone") and ideal environment limits. Interacts with External IoT API to register sensor devices.
- **Sensor Telemetry (`8082`):** The Data Bridge. Runs a scheduled task every 10 seconds to pull data from the external IoT service and pushes it to the Automation service immediately.
- **Automation & Control (`8083`):** The Rule Engine. Evaluates live sensor data against zone thresholds and triggers logging actions (`TURN_FAN_ON`, `TURN_HEATER_ON`).
- **Crop Inventory (`8084`):** Tracks the lifecycle stages of crops (`SEEDLING`, `VEGETATIVE`, `HARVESTED`).

## 🚀 How to Run

Because microservices depend on the infrastructure, you **must** start them in this exact sequence:

1. **Config Server** - Start this first to provide configurations.
2. **Eureka Discovery** - Start next. You can view the dashboard at `http://localhost:8761`.
3. **API Gateway** - Boot up the gateway which handles all routing.
4. **Domain Services** - Finally, start Zone, Sensor, Automation, and Crop services.

### ✅ Service Registry Verification
Once all microservices are running normally, they will register themselves with Eureka. 

![Eureka Dashboard Status](docs/eureka-dashboard.png)
*(Above Image: Eureka dashboard showing API-GATEWAY, AUTOMATION-SERVICE, CONFIG-SERVER, CROP-SERVICE, SENSOR-SERVICE, ZONE-SERVICE status UP)*

## 🔐 Security & Synchronous Communication
- **JWT Authorization:** Secured via API Gateway. All external calls require a valid `Bearer Token` header.
- **Inter-Service Communication:** Uses OpenFeign (or RestTemplate) to allow services (like Automation) to request data synchronously from other services (like Zone).
