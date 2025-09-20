# Ecom.Foundations.Microservices

## Project Overview

Welcome to **Ecom.Foundations.Microservices**, a foundational e-commerce platform built using a microservices architecture. This project serves as a showcase of modern, enterprise-grade software engineering practices, including cloud-native development, event-driven communication, and a **pragmatic polyglot approach**. It's designed to demonstrate a deep understanding of building a resilient, scalable, and observable system.

This repository is the blueprint for a full-featured e-commerce system, including core services for a product catalog, user accounts, shopping carts, and a comprehensive order fulfillment lifecycle.

---

## Architecture

The project's architecture is meticulously documented using the **C4 model**, providing a clear, layered view of the system. You can explore the architecture at different levels of detail by viewing the diagrams in the `docs/architecture` directory.

### C4 Context Diagram
This high-level diagram shows the system as a single block and its interactions with external users and systems, providing the broadest view of the system's place in the world.

### C4 Container Diagram
This diagram zooms in on the system to reveal its internal containers (microservices, databases, and message bus) and their communication channels.

### C4 Deployment Diagram
This diagram shows how the system is deployed onto the infrastructure, with a focus on how containers are grouped and hosted on Azure's managed services.

### Event-Driven Communication
Core business processes, such as the order lifecycle, are coordinated through **event choreography** using **Azure Service Bus**. This asynchronous communication pattern ensures services remain loosely coupled and the system is resilient to individual service failures.

---

## Development Workflow

This project adopts a **disciplined, incremental approach** to development, focusing on delivering stable, well-documented, and production-ready code. The workflow follows an industry-standard process:

1.  **Planning & Design**: Each unit of work begins with a design phase. This includes creating or updating design documents (e.g., C4 Component and Sequence diagrams) to define the specific component's internal architecture and its interactions.
2.  **GitHub Flow**: Work is developed on short-lived feature branches, which are merged into the `main` branch via pull requests after thorough code review. This approach ensures code quality and provides a clear history of changes.
3.  **Test-Driven Development (TDD)**: Code is written with unit and integration tests to ensure correctness and maintainability from the start.
4.  **CI/CD**: Changes pushed to a feature branch automatically trigger a GitHub Actions pipeline, which builds and tests the services. Merging to `main` then triggers an automated deployment pipeline to Azure. This pipeline includes advanced deployment strategies such as **canary deployments** and **blue/green deployments** using Azure Container Apps' revision and traffic splitting features to enable safe, continuous delivery with zero downtime.

---

## Technology Stack

This project uses a **pragmatic polyglot approach**, with each service's technology stack chosen to best fit its specific function.

### By Component

* **Backend For Frontend (BFF)**
    * **Description**: A dedicated service for the public-facing UI. It acts as an API aggregator and customizes responses for specific frontends (e.g., web or mobile). This provides a more tailored and efficient client experience than a general-purpose API gateway.
    * **Technologies**: **ASP.NET Core Minimal API**, **YARP**
* **Catalog Service**
    * **Description**: Core e-commerce service for product management. Publishes `ProductUpdated` events to the message bus.
    * **Architecture**: **Vertical Slice Architecture** with **MediatR**.
    * **Technologies**: **ASP.NET Core Web API**, **PostgreSQL** with **Marten** (used as a document database for flexible product data)
* **Basket Service**
    * **Description**: Manages shopping cart operations and user sessions.
    * **Architecture**: **Vertical Slice Architecture** with **MediatR**.
    * **Technologies**: **ASP.NET Core Web API**, **PostgreSQL** with **Marten**, **Redis** (used for a key-value store to support Marten's document storage)
* **Ordering Service**
    * **Description**: Handles the order lifecycle and transactional operations.
    * **Architecture**: **Clean Architecture**.
    * **Technologies**: **ASP.NET Core Web API**, **Azure SQL Database**
* **Inventory Service**
    * **Description**: A critical service for managing product stock levels, preventing overselling, and reserving items.
    * **Architecture**: **Clean Architecture**.
    * **Technologies**: **ASP.NET Core Web API**, **MediatR**, **PostgreSQL**
* **Identity Service**
    * **Description**: Manages user accounts and handles authentication/authorization.
    * **Architecture**: **Clean Architecture**.
    * **Technologies**: **ASP.NET Core Web API**, **PostgreSQL**
* **Discount / Promotions Service**
    * **Description**: Manages promotion rules and coupon codes. The use of a NoSQL database allows for flexible, schema-less promotion definitions.
    * **Architecture**: **N-Layer Architecture**.
    * **Technologies**: **ASP.NET Core Web API**, **Azure Cosmos DB**
* **Search Service**
    * **Description**: Provides a fast, faceted search for the product catalog. This service is a prime example of the **CQRS (Command Query Responsibility Segregation)** pattern, where the write model (event ingestion) is separate from the read model (search queries).
    * **Architecture**: **Minimal API** for the public-facing API.
    * **Technologies**: **Elasticsearch**
* **Payment Service**
    * **Description**: Handles payment processing and communication with the payment gateway. Chosen for its asynchronous, I/O-heavy nature.
    * **Architecture**: **Minimal API**.
    * **Technologies**: **Node.js**, **Redis**
* **Shipping Service**
    * **Description**: Manages fulfillment and tracks shipping requests. Also uses a non-blocking architecture to handle external API calls.
    * **Architecture**: **Minimal API**.
    * **Technologies**: **Node.js**, **PostgreSQL**
* **Notification Service**
    * **Description**: Handles transactional emails, SMS, and push notifications.
    * **Architecture**: **Minimal API**.
    * **Technologies**: **Node.js**, **Stateless**
* **Reviews & Ratings Service**
    * **Description**: A dedicated service for managing user-generated content like product reviews and ratings.
    * **Architecture**: **Minimal API**.
    * **Technologies**: **Node.js**, **Azure Cosmos DB** (NoSQL for flexible review data)

### Managed & External Services

* **Databases**: **Azure Database for PostgreSQL**, **Azure SQL Database**, **Azure Cosmos DB**
* **Caching**: **Azure Cache for Redis**
* **Messaging**: **Azure Service Bus**
* **Observability**: **Prometheus** (for collecting time-series metrics), **OpenTelemetry** (for distributed tracing), and the **ELK Stack** (Elasticsearch, Logstash, Kibana) for centralized logging and analytics. 
* **Search**: **Elasticsearch**
* **Authentication**: **Azure AD**
* **Payments**: **Stripe**
* **Email**: **SendGrid**
* **CI/CD**: **GitHub Actions**, **GitHub Container Registry**

---

## Getting Started

To get a local development environment running, you'll need the following tools installed on your machine. However, the use of Docker ensures that your local setup is simple and consistent, regardless of your OS.

* **DevOps**: **Docker**

### 1. Set Up the Project

1.  Clone the repository: `git clone https://github.com/TheBramCoder/Ecom.Foundations.Microservices.git`
2.  Navigate to the project directory: `cd Ecom.Foundations.Microservices`

### 2. Run the Microservices

This project uses Docker Compose to run all the microservices and their dependencies (like databases and the message bus) with a single command. The use of Docker ensures that your local environment is consistent and independent of your local machine's specific tool installations.

1.  Ensure Docker Desktop is running on your machine.
2.  From the root directory of the project, run: `docker-compose up --build`

This command will:
* Build the Docker images for all the services.
* Start all the containers for the microservices, databases, and message broker.
* Your services will be accessible at `http://localhost:[port]` as defined in the `docker-compose.yml` file.

You can press `Ctrl + C` in the terminal to stop the containers. To remove all the containers, use `docker-compose down`.

### 3. Explore the Code

The source code for each microservice is located in its own sub-directory.
* **[Service Name]**: `src/[service-name]`

---

## Contributing

Contributions are welcome! Please read the `CONTRIBUTING.md` file for details on our code of conduct and the process for submitting pull requests.

---

## License

This project is licensed under the MIT License - see the `LICENSE.md` file for details.