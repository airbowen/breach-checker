# breach-checker

A secure, scalable application that allows users to check if their email address has been compromised in known data breaches.
Features

Email Verification API: RESTful API to check if an email has been compromised
Caching Layer: Redis-based caching for improved performance
Database Storage: PostgreSQL for persistent storage of compromised emails
Security Measures:

Email hashing using SHA-256
Rate limiting
HTTPS with TLS
Security headers
CORS protection


Containerization: Docker and Kubernetes configurations
Load Balancing: Nginx for reverse proxy and load balancing

## Architecture
                                   ┌─────────────────┐
                                   │                 │
                                ┌──►  Redis Cache    │
                                │  │                 │
                                │  └─────────────────┘
                                │
┌─────────┐     ┌──────────┐    │     ┌─────────────────┐
│         │     │          │    │     │                 │
│ Users   ├────►│  Nginx   ├────┼────►  API Service     │
│         │     │          │    │     │  (Golang)       │
└─────────┘     └──────────┘    │     └────────┬────────┘
                                │              │
                                │              │
                                │     ┌────────▼────────┐
                                │     │                 │
                                └────►│  PostgreSQL DB  │
                                      │                 │
                                      └─────────────────┘
Prerequisites

Docker and Docker Compose
Go 1.18+ (for local development)
kubectl (for Kubernetes deployment)

## Quick Start with Docker Compose

## Clone the repository:
git clone https://github.com/airbowen/breach-checker.git
cd breach-checker

Start the application:
docker-compose -f deploy/docker-compose.yml up -d

Access the web interface:

Open https://localhost in your browser
Accept the self-signed certificate warning (for development only)



## Kubernetes Deployment

Build and push the Docker images:
docker build -t breach-checker-api:latest -f deploy/Dockerfile.api .
docker build -t breach-checker-web:latest -f deploy/Dockerfile.web .

Create a secret for database credentials:
kubectl create secret generic db-credentials --from-literal=url="postgres://postgres:postgres@postgres:5432/breachdb"

Apply Kubernetes manifests:
kubectl apply -f deploy/kubernetes/

Access the application:
kubectl port-forward service/nginx 8443:443
Then visit https://localhost:8443 in your browser

## Development Setup

Install Go dependencies:
go mod download

Start PostgreSQL and Redis locally:
docker-compose -f deploy/docker-compose-dev.yml up -d

Run the API server:
go run api/main.go

For the web interface, you can use any static file server:
cd web && python -m http.server 8000


API Endpoints
Check Email
Endpoint: POST /api/check
Request Body:
json{
  "email": "test@example.com"
}
Success Response:
json{
  "email": "test@example.com",
  "compromised": false,
  "message": "Good news! Your email was not found in our database of compromised accounts."
}
Health Check
Endpoint: GET /api/health
Success Response:
json{
  "status": "ok"
}

## Security Considerations

All email addresses are stored as SHA-256 hashes to protect user privacy
Rate limiting is implemented to prevent brute force attacks
HTTPS is enforced with modern TLS configurations
Security headers protect against common web vulnerabilities
Container security follows best practices

## Scalability Features

Horizontal scaling of API servers
Redis caching reduces database load
Nginx load balancing distributes traffic
Kubernetes configurations for cloud deployment
Database connection pooling

Project Structure
/
├── api/                 # Go API server code
│   ├── handlers/        # Request handlers
│   ├── middleware/      # HTTP middleware
│   ├── models/          # Data models
│   └── utils/           # Utility functions
├── db/
│   └── migrations/      # Database schema and migrations
├── web/                 # Static web files
│   └── assets/          # JavaScript and CSS
├── nginx/               # Nginx configuration
└── deploy/              # Deployment configurations
    ├── docker-compose.yml
    └── kubernetes/      # Kubernetes manifests
