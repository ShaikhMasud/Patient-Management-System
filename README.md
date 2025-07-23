# 🩺 Patient Management System (Microservices)

A fully Dockerized microservices system built with Spring Boot, gRPC/protobuf, Kafka, and PostgreSQL—featuring modular architecture, REST and RPC endpoints, synchronous and asynchronous inter-service communication, and scalable event-driven design.

## 📖 Project Overview

You’ve built a production-style microservice ecosystem with three core services:

- **Patient Service** – handles patient data  
- **Billing Service** – performs billing logic  
- **Analytics Service** – consumes patient events for analysis  

Everything is packaged with Docker and orchestrated using Docker Compose. Communication happens both via gRPC (Patient ↔ Billing) and Kafka (Patient → Analytics), following industry best practices.

## 🛠 Infrastructure & Docker Setup

Docker Compose orchestrates:

- PostgreSQL container for persistence  
- Kafka brokers  
- Each microservice runs in its own Docker container built via respective Dockerfile  
- Services share a custom Docker network for seamless internal communication  
- Configurations (ports, topic names, DB credentials) are managed via environment variables and `pom.xml`

Each service uses **Maven** to manage dependencies, build lifecycle, and compile protobuf definitions where needed.

## Service-by-Service Breakdown

### ✅ Patient Service

- Exposes REST endpoints such as `POST /patients`, `GET /patients/{id}`  
- Publishes events to a Kafka topic (e.g., `patient-events`) on creation/update  
- Sends billing requests via gRPC to the Billing Service  
- Features layered structure: `Controller → Service → Repository → DTOs`  
- Shares the `patient.proto` for generating Java stubs used in Billing Service  
- Uses Maven to handle dependencies for Spring Boot, Kafka, gRPC, and protobuf  
- Protobuf compilation and gRPC client stubs are configured via the Maven protobuf plugin in `pom.xml`

### ✅ Billing Service

- Hosts gRPC RPC endpoints to process billing requests (defined in `billing_service.proto`)  
- Invoked synchronously by Patient Service for billing generation  
- Encapsulates billing logic with DTOs, services, and persistence via its own database  
- Uses Maven for Spring Boot and gRPC dependencies  
- gRPC server stubs are generated through Maven during the build process using the protobuf plugin  

### ✅ Analytics Service

- Subscribed to Kafka topics via configured Kafka Consumer  
- Consumes messages from the Patient Service producer (Patient)  
- Stateless listener service designed to facilitate analytics dashboards or logs later  
- Handles serialization/deserialization and error logging in the consumer layer  
- Maven manages dependencies for Spring Boot, Kafka clients, and JSON processing  

## 🔄 Communication Mechanisms

### gRPC (Protocol Buffers)

- Defined in `*.proto` file  
- Compiled using Maven plugin (`protobuf-maven-plugin`) to generate Java classes  
- Billing Service implements the `ServiceGrpc` interface  
- Patient Service uses gRPC client to invoke billing actions  
- All gRPC dependencies and plugin configurations are managed in each service’s `pom.xml`

### Kafka (Event Streaming)

- Patient Service acts as producer, sends serialized patient events  
- Kafka topic (like `patient-topic`) configured in `application.yml` or through environment variables  
- Analytics Service registers `@KafkaListener` to process incoming events  
- Supports scalability via consumer group configuration (e.g., `groupId`)  
- Kafka client dependencies are handled via Maven

## 🌟 Key Learning Outcomes

- End-to-end Dockerization of microservices + infrastructure  
- Modular code structure using DTOs and layered architecture for maintainability  
- Synchronous communication via gRPC (protobuf-defined)  
- Asynchronous, decoupled messaging via Kafka producer/consumer  
- Experience configuring and orchestrating PostgreSQL, Kafka, Zookeeper, and Docker Compose  
- Effective use of Maven for dependency management, protobuf generation, and service builds

<img width="1600" height="852" alt="image" src="https://github.com/user-attachments/assets/5665738e-ef91-4aca-8785-d8fbf60429fd" />
