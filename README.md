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
- Configurations (ports, topic names, DB credentials) are managed via `.env` or `application.yml`

## Service-by-Service Breakdown

### ✅ Patient Service

- Exposes REST endpoints such as `POST /patients`, `GET /patients/{id}`  
- Publishes events to a Kafka topic (e.g., `patient-events`) on creation/update  
- Sends billing requests via gRPC to the Billing Service  
- Features layered structure: `Controller → Service → Repository → DTOs`  
- Shares the `patient.proto` for generating Java stubs used in Billing Service

### ✅ Billing Service

- Hosts gRPC RPC endpoints to process billing requests (defined in `patient.proto`)  
- Invoked synchronously by Patient Service for billing generation  
- Encapsulates billing logic with DTOs, services, and persistence via its own database

### ✅ Analytics Service

- Subscribed to Kafka topics via configured Kafka Consumer  
- Consumes messages from the Patient Service producer  
- Stateless listener service designed to facilitate analytics dashboards or logs later  
- Handles serialization/deserialization and error logging in the consumer layer

## 🔄 Communication Mechanisms

### gRPC (Protocol Buffers)

- Defined in `*.proto` file  
- Compiled using Maven or Gradle to generate Java classes  
- Billing Service implements the `ServiceGrpc` interface  
- Patient Service uses gRPC client to invoke billing actions

### Kafka (Event Streaming)

- Patient Service acts as producer, sends serialized patient events  
- Kafka topic (like `patient-topic`) configured in `application.yml`  
- Analytics Service registers `@KafkaListener` to process incoming events  
- Supports scalability via consumer group configuration (e.g., `groupId`)

## 🌟 Key Learning Outcomes

- End-to-end Dockerization of microservices + infrastructure  
- Modular code structure using DTOs and layered architecture for maintainability  
- Synchronous communication via gRPC (protobuf-defined)  
- Asynchronous, decoupled messaging via Kafka producer/consumer  
- Experience configuring and orchestrating PostgreSQL, Kafka, Zookeeper, and Docker Compose
