# GateKeeper
Enterprise authentication and authorization infrastructure powered by Keycloak, PostgreSQL, and Traefik reverse proxy.

## Overview

GateKeeper provides a complete identity and access management solution for securing applications and APIs. Built on industry-standard open-source technologies, it delivers enterprise-grade authentication, user management, and authorization capabilities in a containerized, easily deployable architecture.

## Architecture

The system consists of three core components:

- **Traefik**: Reverse proxy and load balancer for routing and SSL/TLS termination
- **Keycloak**: Open-source identity provider for authentication and authorization
- **PostgreSQL**: Relational database for storing authentication credentials and configurations

All services run within a dedicated Docker network to ensure isolated communication.

## Prerequisites

- Docker Engine (version 20.10 or later)
- Docker Compose (version 1.29 or later)
- Minimum 2GB available RAM
- Port availability: 80, 8080

## Quick Start

### 1. Environment Configuration

Create a `.env` file in the project root with the following variables:

```env
# Database Configuration
DB_NAME=keycloak
DB_USER=keycloak_admin
DB_PASSWORD=your_secure_database_password

# Keycloak Admin Credentials
KEYCLOAK_ADMIN_USER=admin
KEYCLOAK_ADMIN_PASSWORD=your_secure_admin_password
```

Security recommendation: Use strong, randomly generated passwords for production deployments.

### 2. Start Services

```bash
docker-compose up -d
```

This command will:
- Pull required container images
- Create the isolated network
- Initialize PostgreSQL with persistent storage
- Start Keycloak in development mode
- Configure Traefik routing

### 3. Access the System

- **Keycloak Console**: http://auth.localhost:8080
- **Traefik Dashboard**: http://localhost:8080/dashboard/
- **Default Admin Account**: Username from `KEYCLOAK_ADMIN_USER` in `.env`

## Service Details

### Traefik (Reverse Proxy)

- Container: `traefik`
- Port: 80, 8080
- Configuration: Dynamic routing via Docker provider
- Role: Routes incoming requests to Keycloak based on hostname rules

### Keycloak (Identity Provider)

- Container: `auth-server`
- Port: 8080 (via Traefik)
- Image: `quay.io/keycloak/keycloak:latest`
- Database: PostgreSQL
- Mode: Development (start-dev)

### PostgreSQL (Data Store)

- Container: `auth-db`
- Image: `postgres:17`
- Default Database: `keycloak`
- Persistent Volume: `pg_data`
- Restart Policy: Unless stopped

## Network Architecture

All services communicate through the `auth-network` bridge network. This ensures isolated, secure inter-service communication without exposing services to the host network directly.

## Management

### Stop Services

```bash
docker-compose down
```

### View Logs

```bash
docker-compose logs -f keycloak
```

### Restart a Service

```bash
docker-compose restart auth-server
```

### Reset Database

```bash
docker-compose down -v
docker-compose up -d
```

Warning: This command will delete all data in the PostgreSQL volume.

## Security Considerations

- Change default credentials in `.env` before production deployment
- Enable HTTPS/TLS for Traefik in production
- Use strong password policies in Keycloak administration console
- Restrict network access to necessary ports only
- Regularly update container images
- Implement database backups for persistent data
- Use secret management tools for production environments

## Production Deployment

For production environments, implement:

1. **SSL/TLS Configuration**: Update Traefik configuration with certificate management
2. **Database Backups**: Configure automated PostgreSQL backup strategies
3. **Resource Limits**: Set CPU and memory constraints per container
4. **Monitoring**: Integrate logging and monitoring solutions
5. **High Availability**: Deploy multiple Keycloak instances with load balancing
6. **Persistent Storage**: Configure managed database services instead of container volumes

## Troubleshooting

### Services fail to start

Check container logs for error messages:
```bash
docker-compose logs
```

Verify all required environment variables are set in `.env`.

### Cannot connect to Keycloak

Ensure Traefik is running and routing correctly. Access Traefik dashboard to verify route configuration.

### Database connection errors

Verify PostgreSQL is fully initialized before Keycloak starts. Add health checks and dependency configurations as needed.

## License
This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Support

For issues, questions, or feature requests, contact the development team.