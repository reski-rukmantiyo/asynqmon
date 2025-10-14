# Asynqmon Project Context

## Project Overview

Asynqmon is a web UI tool for monitoring and administering [Asynq](https://github.com/hibiken/asynq) queues and tasks. It provides a user-friendly interface to view and manage Asynq task queues, including active, pending, scheduled, retry, archived, and completed tasks.

The project consists of:
1. A standalone binary that can be run independently
2. A Go library that can be embedded into existing web applications
3. A React-based frontend UI for visualization and interaction

### Key Features
- Monitor queue statistics and task statuses
- Perform administrative actions (pause/resume queues, cancel/archive/run tasks)
- Support for Prometheus integration to display time-series metrics
- Read-only mode for restricted access
- Task aggregation support
- Responsive web interface with dark mode

## Technology Stack

### Backend
- **Language**: Go (1.16+)
- **Framework**: Standard `net/http` with `gorilla/mux` router
- **Dependencies**:
  - `github.com/hibiken/asynq` - Core Asynq client library
  - `github.com/redis/go-redis/v9` - Redis client
  - `github.com/prometheus/client_golang` - Prometheus metrics
  - `github.com/gorilla/mux` - HTTP router
  - `github.com/rs/cors` - CORS handling

### Frontend
- **Framework**: React 16.x
- **UI Library**: Material-UI (v4)
- **State Management**: Redux Toolkit
- **Charting**: Recharts
- **Build Tool**: React Scripts (Create React App)

### Infrastructure
- **Database**: Redis (used by Asynq)
- **Containerization**: Multi-stage Docker build
- **Deployment**: Standalone binary or Docker container

## Project Structure

```
asynqmon/
├── cmd/asynqmon/     # Main application entry point
├── ui/               # React frontend application
│   ├── public/       # Static assets
│   └── src/          # React source code
├── Dockerfile        # Multi-stage Docker build
├── Makefile          # Build and development commands
├── go.mod            # Go module dependencies
└── *.go              # Go source files for the library
```

## Building and Running

### Prerequisites
- Go 1.16+
- Node.js and Yarn (for frontend assets)
- Redis server (for Asynq)

### Development Commands

```bash
# Build everything including UI assets
make build

# Build only the Go backend (skip UI build)
make api

# Build Docker image
make docker

# Install frontend dependencies
make assets
```

### Running the Application

#### Standalone Binary
```bash
# Build the binary
make build

# Run with defaults (localhost:8080, Redis at 127.0.0.1:6379)
./asynqmon

# Run with custom configuration
./asynqmon --port=3000 --redis-addr=localhost:6380

# Enable Prometheus integration
./asynqmon --enable-metrics-exporter --prometheus-addr=http://localhost:9090
```

#### Docker Container
```bash
# Pull official image
docker pull hibiken/asynqmon

# Run with defaults
docker run --rm --name asynqmon -p 8080:8080 hibiken/asynqmon

# Run with custom configuration
docker run --rm --name asynqmon -p 3000:3000 hibiken/asynqmon --port=3000 --redis-addr=host.docker.internal:6380
```

#### As Library
```go
h := asynqmon.New(asynqmon.Options{
    RootPath: "/monitoring",
    RedisConnOpt: asynq.RedisClientOpt{Addr: ":6379"},
})

// With net/http
http.Handle(h.RootPath()+"/", h)

// With gorilla/mux
r := mux.NewRouter()
r.PathPrefix(h.RootPath()).Handler(h)
```

### Environment Variables
| Variable | Description | Default |
|----------|-------------|---------|
| PORT | Server port | 8080 |
| REDIS_ADDR | Redis server address | 127.0.0.1:6379 |
| REDIS_DB | Redis database number | 0 |
| REDIS_PASSWORD | Redis password | "" |
| REDIS_URL | Redis connection URL | "" |
| REDIS_CLUSTER_NODES | Comma-separated cluster nodes | "" |
| REDIS_TLS | TLS server name | "" |
| REDIS_INSECURE_TLS | Disable TLS cert checks | false |
| ENABLE_METRICS_EXPORTER | Enable Prometheus exporter | false |
| PROMETHEUS_ADDR | Prometheus server address | "" |
| READ_ONLY | Enable read-only mode | false |

## Development Workflow

### Backend Development
1. Go source files are located in the root directory
2. Main entry point is in `cmd/asynqmon/main.go`
3. HTTP handlers are defined in separate `*_handlers.go` files
4. The UI is embedded using Go's `embed` package from `ui/build`

### Frontend Development
1. React source code is in `ui/src/`
2. Build output goes to `ui/build/` which is embedded in the Go binary
3. During development, use `yarn start` in the `ui/` directory for hot reloading
4. Production builds use `yarn build`

### Testing
```bash
# Run Go tests
go test ./...

# Run frontend tests
cd ui && yarn test
```

## Deployment Considerations

### Docker Deployment
The project uses a multi-stage Docker build:
1. Stage 1: Build React frontend assets
2. Stage 2: Build Go binary with embedded assets
3. Stage 3: Create minimal scratch image with the binary

### Configuration
When deploying, consider:
- Redis connection settings
- Port mappings
- Prometheus integration if needed
- Read-only mode for production environments
- TLS configuration for secure connections

### Integration Patterns
1. **Standalone**: Run as a separate service alongside your application
2. **Embedded**: Integrate directly into your Go application as a library
3. **Microservice**: Deploy as a dedicated monitoring service in containerized environments

## Version Compatibility

Asynqmon versions are designed to be compatible with specific Asynq versions:

| Asynq version | Asynqmon version |
|---------------|------------------|
| 0.23.x        | 0.7.x            |
| 0.22.x        | 0.6.x            |
| 0.20.x-0.21.x | 0.5.x            |
| 0.19.x        | 0.4.x            |
| 0.18.x        | 0.2.x-0.3.x      |
| 0.16.x-0.17.x | 0.1.x            |