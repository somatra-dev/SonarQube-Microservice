# PES Expo Microservices

Spring Boot microservices with SonarQube + JaCoCo for code quality and coverage.

## Modules

- `product-service` (port 9002)
- `order-service` (port 9003)
- Root app (`src/`) used for shared Gradle + SonarQube configuration

## Project Structure

```
rootProject/
├── build.gradle
├── settings.gradle
├── docker-compose.sonarqube.yml
├── src/                         # Root Spring Boot app
├── product-service/             # Product microservice
└── order-service/               # Order microservice
```

## Technology Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| Java | 21 | Programming language |
| Spring Boot | 4.0.1 | Application framework |
| Gradle | 9.2.1 | Build tool (wrapper) |
| PostgreSQL | local or Docker | Service databases |
| JaCoCo | 0.8.12 | Code coverage |
| SonarQube | LTS Community | Code quality analysis |

## Prerequisites

- Java 21+
- Docker & Docker Compose (for SonarQube)
- PostgreSQL databases for services (local or Docker)

## Service Databases

The services are configured for local PostgreSQL instances:

- `product-service`: `jdbc:postgresql://localhost:5991/product_db` (user/pass: `product`)
- `order-service`: `jdbc:postgresql://localhost:5992/order_db` (user/pass: `order`)

Update `application.yml` in each service if your DB settings differ.

## Quick Start

### 1) Start SonarQube

```bash
docker compose -f docker-compose.sonarqube.yml up -d
```

SonarQube UI: `http://localhost:9000`
Default credentials: `admin` / `admin`

### 2) Generate a SonarQube Token

- Profile icon → **My Account** → **Security**
- Create a token (example name: `gradle-analysis`)
- Export it in your shell:

```bash
export SONAR_TOKEN=your_token_here
```

### 3) Run Tests + Coverage

```bash
./gradlew clean test
```

### 4) Run SonarQube Analysis

```bash
./gradlew sonar
```

Or run everything in one command:

```bash
SONAR_TOKEN=your_token_here ./gradlew clean test sonar
```

## SonarQube + JaCoCo Configuration

- Root `build.gradle` defines SonarQube + JaCoCo configuration.
- Subprojects inherit JaCoCo reports and set per-module report paths.
- `sonar` task depends on service tests and the aggregated root report.

## API Endpoints

### Product Service (`http://localhost:9002`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/products` | Create a product |
| GET | `/api/v1/products` | Get all products |
| GET | `/api/v1/products/{uuid}` | Get product by UUID |
| PUT | `/api/v1/products/{uuid}` | Update product |
| DELETE | `/api/v1/products/{uuid}` | Delete product |
| GET | `/api/v1/products/{uuid}/orders` | Get product with orders |

### Order Service (`http://localhost:9003`)

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/orders` | Create an order |
| GET | `/api/v1/orders` | Get all orders |
| GET | `/api/v1/orders/{uuid}` | Get order by UUID |
| GET | `/api/v1/orders/product/{productUuid}` | Get orders by product |
| DELETE | `/api/v1/orders/{uuid}` | Delete order |

## Reports

| Report | Location |
|--------|----------|
| Root JaCoCo XML | `build/reports/jacoco/jacocoRootReport/jacocoRootReport.xml` |
| Root JaCoCo HTML | `build/reports/jacoco/jacocoRootReport/html/index.html` |
| Module JaCoCo XML | `*/build/reports/jacoco/test/jacocoTestReport.xml` |
| Module JaCoCo HTML | `*/build/reports/jacoco/test/html/index.html` |
| Test Results | `*/build/reports/tests/test/index.html` |

## Common Commands

```bash
# Start SonarQube
docker compose -f docker-compose.sonarqube.yml up -d

# Stop SonarQube
docker compose -f docker-compose.sonarqube.yml down

# Run all tests
./gradlew test

# Run tests for specific module
./gradlew :product-service:test
./gradlew :order-service:test

# Generate aggregated root coverage report
./gradlew jacocoRootReport

# Run SonarQube analysis
SONAR_TOKEN=your_token ./gradlew sonar
```

## Troubleshooting

### SonarQube Authentication Error

- Verify your token in `http://localhost:9000`
- Ensure `SONAR_TOKEN` is exported in your shell

### SonarQube Not Starting

```bash
lsof -i :9000
```

```bash
docker logs sonarqube
```

### Gradle Version

This repo uses the Gradle wrapper:

```bash
./gradlew --version
```
