# SwiftLogistics - Setup Guide

This guide will help you clone, setup, and run the SwiftLogistics middleware architecture project in IntelliJ IDEA.

## 🚀 Step-by-Step Setup

### Step 1: Clone the Repository

1. Open terminal/command prompt
2. Navigate to your desired directory (e.g., `Desktop` or `Documents`)
3. Clone the repository:

```bash
git clone <repository-url>
cd SwiftLogistics
```

### Step 2: Setup Database (PostgreSQL)

1. **Install and Start PostgreSQL**
   - Windows: Use the installer and start PostgreSQL service
   - macOS: `brew install postgresql && brew services start postgresql`
   - Linux: `sudo apt-get install postgresql postgresql-contrib`

2. **Create Databases**

Open PostgreSQL command line (psql) or pgAdmin and create databases:

```sql
-- Create databases for each service
CREATE DATABASE swiftlogistics_client;
CREATE DATABASE swiftlogistics_route;
CREATE DATABASE swiftlogistics_warehouse;
CREATE DATABASE swiftlogistics_esb;

-- Create user (optional)
CREATE USER swiftlogistics WITH PASSWORD 'password123';
GRANT ALL PRIVILEGES ON DATABASE swiftlogistics_client TO swiftlogistics;
GRANT ALL PRIVILEGES ON DATABASE swiftlogistics_route TO swiftlogistics;
GRANT ALL PRIVILEGES ON DATABASE swiftlogistics_warehouse TO swiftlogistics;
GRANT ALL PRIVILEGES ON DATABASE swiftlogistics_esb TO swiftlogistics;
```

### Step 3: Setup RabbitMQ

1. **Install RabbitMQ**
   - Windows: Download and install from official website
   - macOS: `brew install rabbitmq`
   - Linux: `sudo apt-get install rabbitmq-server`

2. **Start RabbitMQ**
   - Windows: Start from Services or command line
   - macOS/Linux: `sudo rabbitmq-server`

3. **Enable Management Plugin (Optional)**
```bash
sudo rabbitmq-plugins enable rabbitmq_management
```
Access management UI at: http://localhost:15672 (guest/guest)

### Step 4: Open Project in IntelliJ IDEA

1. **Launch IntelliJ IDEA**

2. **Open Project**
   - Click "Open" or "Open or Import"
   - Navigate to the cloned `SwiftLogistics` folder
   - Select the root folder and click "OK"

3. **Import as Maven Project**
   - IntelliJ should automatically detect this as a Maven project
   - If prompted, choose "Import Maven project automatically"
   - Wait for indexing to complete

### Step 5: Configure Project Structure

1. **Set Project SDK**
   - Go to `File` → `Project Structure` → `Project`
   - Set Project SDK to Java 17
   - Set Project language level to "17 - Sealed types, always-strict floating-point semantics"

2. **Configure Modules**
   - In Project Structure → `Modules`
   - You should see 4 modules:
     - Client-Management-service
     - Route-Optimisation-service  
     - Warehouse-Management-service
     - ESB-Integration-service

### Step 6: Update Configuration Files

Update the database connection settings in each service:

#### Client-Management-service
Edit `Client-Management-service/src/main/resources/application.properties`:
```properties
server.port=8081
spring.application.name=client-management-service

# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/swiftlogistics_client
spring.datasource.username=postgres
spring.datasource.password=your_password_here
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

#### Route-Optimisation-service
Edit `Route-Optimisation-service/src/main/resources/application.properties`:
```properties
server.port=8082
spring.application.name=route-optimisation-service

# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/swiftlogistics_route
spring.datasource.username=postgres
spring.datasource.password=your_password_here
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

#### Warehouse-Management-service
Edit `Warehouse-Management-service/src/main/resources/application.properties`:
```properties
server.port=8083
spring.application.name=warehouse-management-service

# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/swiftlogistics_warehouse
spring.datasource.username=postgres
spring.datasource.password=your_password_here
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

#### ESB-Integration-service
The ESB service configuration is already properly set up.

## 🏃‍♂️ Running the Services

### Method 1: Using IntelliJ Run Configurations

1. **Create Run Configurations for each service:**

   **For Client-Management-service:**
   - Right-click on `ClientManagementServiceApplication.java`
   - Select "Run 'ClientManagementServiceApplication'"
   - Or create a new configuration: `Run` → `Edit Configurations` → `+` → `Application`
   - Name: "Client Management Service"
   - Main class: `com.example.Client_Management_service.ClientManagementServiceApplication`
   - Module: Client-Management-service

   **Repeat for other services:**
   - Route Optimisation Service (port 8082)
   - Warehouse Management Service (port 8083)
   - ESB Integration Service (port 8085)

2. **Start Services in Order:**
   1. Start ESB Integration Service first
   2. Start other services in any order

### Method 2: Using Maven in Terminal

Open terminal in IntelliJ (`View` → `Tool Windows` → `Terminal`):

```bash
# Start ESB Integration Service (in separate terminal)
cd ESB-Integration-service
./mvnw spring-boot:run

# Start Client Management Service (in separate terminal)
cd Client-Management-service  
./mvnw spring-boot:run

# Start Route Optimisation Service (in separate terminal)
cd Route-Optimisation-service
./mvnw spring-boot:run

# Start Warehouse Management Service (in separate terminal)
cd Warehouse-Management-service
./mvnw spring-boot:run
```

## 🔍 Verify Setup

### Check Services are Running

Open your browser and verify each service:

- **Client Management Service**: http://localhost:8081/actuator/health
- **Route Optimisation Service**: http://localhost:8082/actuator/health
- **Warehouse Management Service**: http://localhost:8083/actuator/health
- **ESB Integration Service**: http://localhost:8085/actuator/health

### Check Logs

In IntelliJ, check the console output for each running service. You should see:
- Spring Boot startup messages
- Database connection confirmations
- No error messages
