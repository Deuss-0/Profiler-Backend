# Docker Deployment Guide for Deuss Backend

This comprehensive guide explains how to deploy and manage the Deuss application using Docker and Docker Compose.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Deployment](#deployment)
- [Database Management](#database-management)
- [Container Management](#container-management)
- [Monitoring and Logging](#monitoring-and-logging)
- [Security Considerations](#security-considerations)
- [Troubleshooting](#troubleshooting)
- [Production Best Practices](#production-best-practices)

## Prerequisites

Before deploying the application, ensure you have the following installed:

- Docker Engine (version 20.10.0 or later)
- Docker Compose (version 2.0.0 or later)
- Git (for cloning the repository)
- Basic understanding of Docker concepts and commands

### System Requirements
- Linux/Unix-based system or Windows with WSL2
- Minimum 2GB RAM
- 20GB free disk space
- CPU with virtualization support

## Installation

1. Clone the repository:
```bash
git clone https://github.com/deuss-space/Profiler-Backend.git
cd Profiler-Backend
```

2. Configure Docker Compose:


## Configuration

### Docker Compose Configuration

The `docker-compose.yml` file defines three services:

1. **app**: The main application container
   ```yaml
   app:
     build: .
     ports:
       - "3000:3000"
     environment:
       - DATABASE_URL=postgresql://postgres:your_password@db:5432/deuss
       - JWT_SECRET=your_jwt_secret
       - SESSION_SECRET=your_session_secret
       - NODE_ENV=production
       - PORT=3000
       - FRONTEND_URL=https://deuss.space
       - RESEND_API_KEY=your_resend_api_key
       - SMTP_FROM=noreply@deuss.space
     depends_on:
       - db
     volumes:
       - ./:/usr/src/app
       - /usr/src/app/node_modules
   ```

2. **db**: PostgreSQL database container
   ```yaml
   db:
     image: postgres:15
     environment:
       - POSTGRES_USER=postgres
       - POSTGRES_PASSWORD=your_password
       - POSTGRES_DB=deuss
     volumes:
       - postgres_data:/var/lib/postgresql/data
   ```

3. **pgadmin**: Database management interface(Not by default)
   ```yaml
   pgadmin:
     image: dpage/pgadmin4
     environment:
       - PGADMIN_DEFAULT_EMAIL=admin@deuss.space
       - PGADMIN_DEFAULT_PASSWORD=your_pgadmin_password
     ports:
       - "5050:80"
     depends_on:
       - db
   ```

### Required Environment Variables

Configure these variables directly in your `docker-compose.yml`:

 ### Change if needed otherwise everything is configured for local setup

- `DATABASE_URL`: PostgreSQL connection string
- `JWT_SECRET`: Secret key for JWT token generation
- `SESSION_SECRET`: Secret key for session management
- `NODE_ENV`: Environment (development/production)
- `PORT`: Application port (default: 3002)


### Optional Environment Variables

Add these as needed:
- `DEBUG_SESSION`: Enable session debugging (true/false)
- `EMAIL_VERIFICATION`: Enable email verification (true/false)
- `COOKIE_DOMAIN`: Domain for cookie settings
- `TWITTER_BEARER_TOKEN`: Twitter API bearer token
- `FRONTEND_URL`: URL of the frontend application
- `RESEND_API_KEY`: API key for Resend email service
- `SMTP_FROM`: Sender email address

## Deployment

### Development Deployment

1. Start the containers:
```bash
docker-compose up -d
```



2. View application logs:
```bash
docker-compose logs -f [service-name]
```

### Production Deployment

1. Configure production settings in docker-compose.yml:
```yaml
app:
  environment:
    - NODE_ENV=production
    # Add other production-specific environment variables
```

2. Build and start containers:
```bash
docker-compose up -d --build
```

3. Verify deployment:
```bash
curl http://localhost:3000/api/health
```

## Database Management

### Initialization
The database is automatically initialized on first run. To manually initialize:

```bash
docker-compose exec app npm run setup-db
```

### Backup
Create a database backup:
```bash
docker-compose exec db pg_dump -U postgres deuss > backup.sql
```

### Restore
Restore from backup:
```bash
cat backup.sql | docker-compose exec -T db psql -U postgres deuss
```

## Container Management

### Common Commands

- Start containers:
```bash
docker-compose up -d
```

- Stop containers:
```bash
docker-compose down
```

- Rebuild containers:
```bash
docker-compose up -d --build
```

- View logs:
```bash
docker-compose logs -f [service_name]
```

- Access container shell:
```bash
docker-compose exec [service_name] sh
```

## Monitoring and Logging

### Application Logs
- View real-time logs:
```bash
docker-compose logs -f app
```

- View specific service logs:
```bash
docker-compose logs -f db
```

### Health Checks
The application includes a health check endpoint:
```bash
curl http://localhost:3000/api/health
```

## Security Considerations

1. **Docker Compose Security**
   - Use Docker secrets for sensitive data
   - Implement proper network isolation
   - Use non-root users in containers

2. **Network Security**
   - Use internal Docker networks
   - Expose only necessary ports
   - Implement proper firewall rules

3. **Database Security**
   - Use strong passwords
   - Limit database access
   - Regular security updates

4. **Application Security**
   - Keep dependencies updated
   - Implement rate limiting
   - Use HTTPS in production

## Troubleshooting

### Common Issues

1. **Container Won't Start**
   - Check logs: `docker-compose logs app`
   - Verify environment variables in docker-compose.yml
   - Check port conflicts

2. **Database Connection Issues**
   - Verify DATABASE_URL in docker-compose.yml
   - Check database logs
   - Ensure database is running

3. **Email Service Issues**
   - Verify Resend API key in docker-compose.yml
   - Check domain verification
   - Review email service logs

### Debugging

Enable debug mode by adding to docker-compose.yml:
```yaml
app:
  environment:
    - DEBUG_SESSION=true
```

## Production Best Practices

1. **High Availability**
   - Use Docker Swarm or Kubernetes
   - Implement load balancing
   - Set up proper monitoring

2. **Performance**
   - Use production-optimized images
   - Implement caching
   - Configure proper resource limits

3. **Maintenance**
   - Regular backups
   - Security updates
   - Performance monitoring

4. **Scaling**
   - Horizontal scaling with Docker Swarm
   - Database replication
   - Load balancer configuration

## Support

For issues and support:
- Create an issue in the repository
- Contact the development team
- contact@deuss.space

## License

This project is licensed under the MIT License - see the LICENSE file for details. 