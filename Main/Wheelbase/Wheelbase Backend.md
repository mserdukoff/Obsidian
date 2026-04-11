# Wheelbase-Go Backend

This project is a high-performance reimplementation of the original Wheelbase FastAPI backend in the Go programming language. It provides a robust REST API for managing vehicle auction runlists, processing CSV uploads, and delivering real-time demand insights for vehicle inventory. The service is designed to integrate seamlessly with a Supabase (PostgreSQL) database.

---

## Core Features

- **High-Performance Runlist Management**: Ingest vehicle data from CSV files with a highly efficient processing pipeline.  
- **Automatic Vehicle Deduplication**: Intelligently deduplicates vehicles by VIN during the import process.  
- **Real-time Demand Insights**: An in-memory caching system provides rapid inventory analysis against predefined targets.  
- **Intelligent Vehicle Categorization**: A flexible rules engine categorizes vehicles to power the demand insights system.  
- **Robust & Scalable Architecture**: Built using a clean, layered architecture for maintainability and testability.  

---

## Architecture Overview

The application is built using a Layered (Clean) Architecture to separate concerns. This design ensures that the core business logic is independent of the web framework and database, making the system easier to maintain, scale, and test.

The request flows from the API layer down to the Repository and back, with dependencies pointing strictly inwards:

- **API Layer (Handlers)**: Manages HTTP requests and responses using Gin. It's responsible for web-specific tasks.  
- **Service Layer (Business Logic)**: Contains the core application logic, such as the CSV processing algorithm and the demand insights calculations.  
- **Repository Layer (Data Access)**: Handles all database communication, including writing SQL queries and managing the pgx connection pool.  

---

## Technology Stack

| Component          | Technology                             |
|--------------------|-----------------------------------------|
| Language           | Go (1.21+)                             |
| Web Framework      | Gin Gonic                              |
| Database Driver    | pgx (High-performance PostgreSQL driver)|
| Configuration      | Viper                                  |
| Data Validation    | go-playground/validator                |
| In-Memory Caching  | go-cache                               |

---

## Project Structure

wheelbase-go/
├── cmd/api/ # Main application entry point & DI wiring
│ └── main.go
├── internal/ # All core application logic
│ ├── api/ # HTTP handlers/controllers
│ ├── service/ # Business logic layer
│ ├── repository/ # Database interaction layer
│ └── models/ # Go struct definitions (DTOs)
├── go.mod # Go module definitions
├── go.sum # Dependency checksums
├── Makefile # Development scripts
└── .env # Environment variables (not committed)

---

## Getting Started

### Prerequisites
- Go (version 1.21 or later)  
- A running PostgreSQL instance (e.g., from Supabase)  
- `make` (optional, for using Makefile shortcuts)  

### Installation & Setup

**Clone the repository:**
```bash
git clone https://github.com/your-username/wheelbase-go.git
cd wheelbase-go

Configure Environment Variables:
Create a .env file in the root directory by copying the example file:

cp .env.example .env
Edit the .env file with your Supabase/PostgreSQL connection string:

# .env
# Example: postgres://postgres:[YOUR-PASSWORD]@[AWS-ENDPOINT].supabase.co:5432/postgres
SUPABASE_URL="your-database-connection-string"

# Port for the API server to run on
PORT=8000
Install Dependencies:

bash
Copy code
go mod tidy
Running the Application
To run the development server (auto-restart on file changes):

bash
Copy code
make run
Alternatively, without make:

bash
Copy code
go run ./cmd/api/main.go
To build a production binary:

bash
Copy code
make build
The compiled binary will be located at ./bin/wheelbase.

Running Tests
Run all unit and integration tests:

bash
Copy code
make test
Alternatively, without make:

bash
Copy code
go test ./...
API Endpoints
Runlists
Method	Path	Description
POST	/runlists/	Creates a new runlist from a CSV file upload.
GET	/runlists/	Retrieves a list of all runlists.
GET	/runlists/{id}	Retrieves a specific runlist by its ID.
PUT	/runlists/{id}	Updates a specific runlist.
DELETE	/runlists/{id}	Deletes a runlist and its associated data.

Example POST /runlists/:
This endpoint expects a multipart/form-data request with a form field named file containing the CSV.

Demand Insights
Method	Path	Description
GET	/demand-insights/	Returns the full demand insights data.
GET	/demand-insights/summary	Returns only the summary statistics.
GET	/demand-insights/health	A health check endpoint for the service.

Contributing
Contributions are welcome! Please feel free to submit a pull request or open an issue for bugs, feature requests, or improvements.

License
This project is licensed under the MIT License. See the LICENSE file for details.