# SwiftLogistics - Middleware Architecture Solution
**IS3108/SCS3203 Assignment 4**

## 📋 Project Overview

SwiftLogistics is a middleware architecture solution designed for a rapidly growing logistics company in Sri Lanka. This project integrates three critical systems to create a unified "SwiftTrack" platform for seamless logistics operations.

## 🎯 Business Problem

Swift Logistics (Pvt) Ltd. needs to replace their siloed, manual systems with a modern, integrated platform that connects:

1. **Client Management System (CMS)** - Legacy SOAP-based XML API for client contracts and billing
2. **Route Optimisation System (ROS)** - Cloud-based RESTful API for efficient route planning  
3. **Warehouse Management System (WMS)** - Proprietary TCP/IP messaging for real-time package tracking

## 🏗️ Architecture Components

### System Architecture Diagram

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Client Portal │    │ Driver Mobile   │    │  Admin Dashboard│
│    (React)      │    │   App (React    │    │    (React)      │
│                 │    │    Native)      │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
┌─────────────────────────────────────────────────────────────────┐
│                     API Gateway                                 │
│                   (Spring Cloud Gateway)                       │
└─────────────────────────────────────────────────────────────────┘
                                 │
         ┌───────────────────────┼───────────────────────┐
         │                       │                       │
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Authentication  │    │ Order Processing│    │  Notification   │
│   Service       │    │    Service      │    │    Service      │
│ (Spring Boot)   │    │ (Spring Boot)   │    │ (Spring Boot)   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                 │
                    ┌────────────┼────────────┐
                    │            │            │
┌─────────────────────────────────────────────────────────────────┐
│                 ESB Integration Service                         │
│                   (Apache Camel + Spring Boot)                 │
│  ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐   │
│  │  CMS Adapter    │ │  ROS Adapter    │ │  WMS Adapter    │   │
│  │ (SOAP Client)   │ │ (REST Client)   │ │ (TCP Client)    │   │
│  └─────────────────┘ └─────────────────┘ └─────────────────┘   │
│                          │                                     │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │            Message Broker (RabbitMQ)                   │   │
│  │     ┌─────────┐ ┌─────────┐ ┌─────────┐               │   │
│  │     │ Orders  │ │ Status  │ │ Notify  │               │   │
│  │     │ Queue   │ │ Queue   │ │ Queue   │               │   │
│  │     └─────────┘ └─────────┘ └─────────┘               │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                                 │
         ┌───────────────────────┼───────────────────────┐
         │                       │                       │
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Legacy CMS      │    │ External ROS    │    │ Legacy WMS      │
│ (SOAP/XML)      │    │ (REST/JSON)     │    │ (TCP/IP)        │
│ On-Premise      │    │ Cloud-Based     │    │ Proprietary     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### Core Services
- **Client-Management-service**: Handles client data, contracts, and order intake
- **Route-Optimisation-service**: Provides route optimization and delivery planning
- **Warehouse-Management-service**: Manages warehouse operations and package tracking
- **ESB-Integration-service**: **NEW** - Orchestrates communication between all systems

### ESB Features
- **Protocol Translation**: Converts between SOAP/XML, REST/JSON, and TCP/IP protocols
- **Message Routing**: Intelligent routing of messages between microservices and legacy systems
- **Asynchronous Processing**: Message queues for high-volume, non-blocking operations
- **Real-time Updates**: Live status tracking and notifications
- **Error Handling**: Retry mechanisms and dead letter queues
- **System Monitoring**: Health checks and connectivity monitoring

## 🔧 Technology Stack

- **Framework**: Spring Boot (Java)
- **ESB**: Apache Camel with Spring Boot
- **Message Broker**: RabbitMQ
- **Communication**: REST APIs, SOAP services, TCP/IP messaging
- **Database**: PostgreSQL
- **Security**: Spring Security with JWT authentication

## 📊 Architectural Challenges Addressed

1. **Heterogeneous Systems Integration**
   - Protocol translation between SOAP/XML, REST/JSON, and TCP/IP
   - Data format standardization across systems

2. **Real-time Tracking & Notifications**
   - Immediate status updates for clients and drivers
   - Push notification system for urgent changes

3. **High-Volume Asynchronous Processing**
   - Non-blocking order processing
   - Scalable message handling during peak periods

4. **Transaction Management**
   - Distributed transaction consistency
   - Failure recovery mechanisms

5. **Scalability & Resilience**
   - Horizontal scaling capabilities
   - Fault-tolerant architecture design

6. **Security**
   - Secure API communication
   - Data protection and access control

## 🚀 Getting Started

### Prerequisites
- Java 17+
- Maven 3.6+
- RabbitMQ Server
- PostgreSQL Database

### Running the Services

Each service can be run independently:

```bash
# ESB Integration Service (Start this first)
cd ESB-Integration-service
./mvnw spring-boot:run

# Client Management Service
cd Client-Management-service
./mvnw spring-boot:run

# Route Optimisation Service  
cd Route-Optimisation-service
./mvnw spring-boot:run

# Warehouse Management Service
cd Warehouse-Management-service
./mvnw spring-boot:run
```

### Service Ports
- ESB Integration Service: `http://localhost:8085`
- Client Management Service: `http://localhost:8081`
- Route Optimisation Service: `http://localhost:8082`
- Warehouse Management Service: `http://localhost:8083`

## 📁 Project Structure

```
SwiftLogistics/
├── README.md
├── ESB-Integration-service/                           # NEW ESB SERVICE
│   ├── src/main/java/com/swiftlogistics/esb/
│   │   ├── ESBIntegrationServiceApplication.java
│   │   ├── adapters/
│   │   │   └── LegacySystemAdapter.java
│   │   ├── config/
│   │   │   ├── MessageBrokerConfig.java
│   │   │   └── ESBMonitoringConfig.java
│   │   └── routes/
│   │       ├── OrderProcessingRoute.java
│   │       └── RealTimeTrackingRoute.java
│   ├── src/main/resources/
│   │   ├── application.properties
│   │   └── wsdl/
│   │       └── cms-service.wsdl
│   └── pom.xml
├── Client-Management-service/
│   ├── src/main/java/com/example/Client_Management_service/
│   │   ├── Controllers/
│   │   ├── Entity/
│   │   ├── Repository/
│   │   ├── Services/
│   │   └── Config/
│   └── pom.xml
├── Route-Optimisation-service/
│   ├── src/main/java/com/example/Route_Optimisation_service/
│   │   ├── Controllers/
│   │   ├── Entity/
│   │   ├── Repository/
│   │   ├── Services/
│   │   └── Config/
│   └── pom.xml
└── Warehouse-Management-service/
    ├── src/main/java/com/example/Warehouse_Management_service/
    │   ├── Controllers/
    │   ├── Entity/
    │   ├── Repository/
    │   ├── Services/
    │   └── Config/
    └── pom.xml
```

## 🔄 Integration Flow

1. **Order Submission**: Client submits order through portal
2. **Order Processing**: CMS processes and validates order
3. **Warehouse Assignment**: WMS receives and tracks package
4. **Route Planning**: ROS optimizes delivery routes
5. **Driver Assignment**: Routes assigned to drivers via mobile app
6. **Real-time Updates**: Status updates propagated across all systems
7. **Delivery Confirmation**: Proof of delivery captured and recorded

## 📚 Documentation Structure

This project includes:
- **Solution Documentation**: Comprehensive architectural design and implementation details
- **Source Code**: Complete implementation of all microservices
- **Presentation**: Video demonstration of the solution architecture

## 🔒 Security Considerations

- API authentication and authorization
- Secure communication protocols
- Data encryption in transit and at rest
- Access control and audit logging
