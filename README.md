# Sellora Service Registry

A **service discovery server** built on **Netflix Eureka**. It acts as the central directory for all microservices in the **Sellora** architecture, enabling dynamic service discovery, load balancing, and inter-service communication without hardcoded URLs.

-----

## 📋 Table of Contents

  - [Overview](https://www.google.com/search?q=%23overview)
  - [Architecture](https://www.google.com/search?q=%23architecture)
  - [Tech Stack](https://www.google.com/search?q=%23tech-stack)
  - [Project Structure](https://www.google.com/search?q=%23project-structure)
  - [Dependencies](https://www.google.com/search?q=%23dependencies)
  - [Configuration](https://www.google.com/search?q=%23configuration)
  - [Service Registration](https://www.google.com/search?q=%23service-registration)
  - [Service Discovery](https://www.google.com/search?q=%23service-discovery)
  - [Setup & Installation](https://www.google.com/search?q=%23setup--installation)
  - [Building & Running](https://www.google.com/search?q=%23building--running)
  - [Eureka Dashboard](https://www.google.com/search?q=%23eureka-dashboard)
  - [Management Endpoints](https://www.google.com/search?q=%23management-endpoints)
  - [Monitoring & Health Checks](https://www.google.com/search?q=%23monitoring--health-checks)
  - [Troubleshooting](https://www.google.com/search?q=%23troubleshooting)

-----

## 🎯 Overview

The **Service Registry** is the cornerstone of service discovery in the **Sellora** microservices ecosystem. It:

  - ✅ Maintains a **dynamic registry** of all available microservices
  - ✅ Enables **automatic service discovery** by name rather than hardcoded IPs
  - ✅ Provides **load balancing** across multiple instances of the same service
  - ✅ Detects **service failures** via periodic health checks
  - ✅ Deregisters **unhealthy services** automatically
  - ✅ Provides a **web dashboard** for viewing registered services

**Application Name:** `service-registry`  
**Default Port:** `9001`  
**Artifact ID:** `Service-Registry`  
**Group ID:** `lk.ijse.eca`

-----

## 🏗️ Architecture

The Service Registry sits at the heart of the **Sellora** discovery ecosystem:

```
┌──────────────────────────────────────────────────┐
│    Netflix Eureka Service Registry               │
│    (Port 9001)                                   │
│                                                  │
│  ┌────────────────────────────────────────────┐ │
│  │ Service Registry Database                  │ │
│  │ - user-service (8001)                      │ │
│  │ - item-service (8002)                      │ │
│  │ - order-service (8003)                     │ │
│  │ - api-gateway (7000)                       │ │
│  └────────────────────────────────────────────┘ │
└──────┬───────────────────────────────────────────┘
       │ HTTP/REST
       │
   ┌───┴─────┬──────────┬──────────┐
   │         │          │          │
   ▼         ▼          ▼          ▼
┌─────┐ ┌──────┐ ┌──────┐ ┌──────┐
│API  │ │User  │ │Item  │ │Order │
│Gate │ │Svc   │ │Svc   │ │Svc   │
│way  │ └──────┘ └──────┘ └──────┘
└─────┘
(All Sellora services register & query the registry)
```

-----

## 📝 Service Registration

### How Sellora Services Register

When any **Sellora** microservice (user-service, item-service, order-service, api-gateway) starts:

1.  **Service reads configuration** from Config-Server.
2.  **Service finds Eureka location** (from config: `http://localhost:9001/eureka`).
3.  **Service sends registration request** with its metadata (IP, Port, Health URL).
4.  **Eureka stores registration** in the registry database.
5.  **Service sends periodic heartbeats** every 30 seconds to stay registered.

-----

## 🔍 Service Discovery

### How Services Are Discovered

When the **Sellora API Gateway** needs to call a microservice:

1.  **Gateway queries Eureka** for the service location by name (e.g., `ITEM-SERVICE`).
2.  **Eureka returns a list** of healthy instances.
3.  **Gateway picks one instance** using round-robin load balancing.
4.  **Gateway routes the request** to the chosen instance.

-----

## 🚀 Setup & Installation

### Prerequisites

  - **Java 21**
  - **Maven 3.6+**
  - **Config-Server running** (port 9000) - **Required to start first\!**

### Installation Steps

1.  **Clone the repository**

    ```bash
    git clone <repository-url>
    cd Sellora-Platform/service-registry
    ```

2.  **Ensure Config-Server is running**

    ```bash
    cd ../config-server
    ./mvnw spring-boot:run
    ```

3.  **Build and Run the Registry**

    ```bash
    cd ../service-registry
    ./mvnw clean install
    ./mvnw spring-boot:run
    ```

-----

## 🎨 Eureka Dashboard

Once running, access the **Sellora** service map at:
**`http://localhost:9001`**

### Example Dashboard View

```
Application: USER-SERVICE     Status: UP (1) - 192.168.1.10:8001
Application: ITEM-SERVICE     Status: UP (1) - 192.168.1.10:8002
Application: API-GATEWAY      Status: UP (1) - 192.168.1.10:7000
```

-----

## 🔄 Service Lifecycle States

```
┌──────────────┐      ┌──────────┐      ┌───────────────┐
│ Registering  │ ───► │    UP    │ ───► │ OUT_OF_SERVICE│
└──────────────┘      └────┬─────┘      └───────┬───────┘
                           │                    │
                           ▼                    ▼
                    ┌──────────┐         ┌──────────────┐
                    │   DOWN   │  ───►   │    Evicted   │
                    └──────────┘         └──────────────┘
```

-----

## 🤝 Support

For issues or questions regarding the **Sellora** infrastructure:

1.  Verify the startup order: **Config Server \> Service Registry \> Gateway**.
2.  Check the logs in the `target/` folder.
3.  Ensure all services are pointing to the correct Eureka URL in their `application.yaml`.

-----

Would you like me to help you configure the **Self-Preservation** settings for your production environment to make the registry more stable?
