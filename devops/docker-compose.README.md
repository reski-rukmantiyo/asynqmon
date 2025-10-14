# Docker Compose Setup for Asynqmon

This docker-compose setup provides a complete environment for running Asynqmon with its dependencies.

## Services Included

1. **Redis** - Required for Asynq task queue storage
2. **Asynqmon** - Web UI for monitoring and administering Asynq tasks
3. **Asynqmon-local** - Builds and runs Asynqmon from local source (profile: local)
4. **Prometheus** - Metrics collection (profile: metrics)
5. **Asynqmon-with-prometheus** - Asynqmon with Prometheus integration (profile: metrics)
6. **Grafana** - Advanced metrics visualization (profile: metrics)

## Quick Start

### Basic Setup (Redis + Asynqmon)
```bash
docker-compose up -d redis asynqmon
```

Access Asynqmon at http://localhost:8080

### Local Development Setup
```bash
docker-compose --profile local up -d redis asynqmon-local
```

Access Asynqmon (built from local source) at http://localhost:8081

### Full Setup with Metrics
```bash
docker-compose --profile metrics up -d
```

Access services at:
- Asynqmon with Prometheus: http://localhost:8082
- Prometheus: http://localhost:9090
- Grafana: http://localhost:3000 (admin/admin)

## Configuration

The Asynqmon service is configured to connect to Redis at `redis:6379` by default.

To customize the configuration, you can modify the docker-compose.yml file or override settings using environment variables.

## Profiles

- **default**: Runs Redis and Asynqmon only
- **local**: Includes services built from local source code
- **metrics**: Includes Prometheus, Grafana, and Asynqmon with metrics integration

Use `--profile <profile-name>` to enable specific profiles.