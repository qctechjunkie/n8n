# n8n Docker Setup

This repository contains a Docker Compose configuration for running [n8n](https://n8n.io/), a powerful workflow automation tool.

## Prerequisites

- Docker Engine 20.10.0 or higher
- Docker Compose 2.0.0 or higher

## Quick Start

1. Clone this repository or copy the files to your desired location

2. Copy the example environment file and configure it:
   ```bash
   cp .env.example .env
   ```

3. Edit the `.env` file and update the following **critical** settings:
   - `N8N_BASIC_AUTH_PASSWORD` - Change from default
   - `N8N_ENCRYPTION_KEY` - Generate with: `openssl rand -hex 32`
   - `GENERIC_TIMEZONE` and `TZ` - Set to your timezone

4. Start n8n:
   ```bash
   docker-compose up -d
   ```

5. Access n8n at: `http://localhost:5678`

6. Login with the credentials from your `.env` file:
   - Username: Value of `N8N_BASIC_AUTH_USER` (default: admin)
   - Password: Value of `N8N_BASIC_AUTH_PASSWORD`

## Configuration

### Environment Variables

All configuration is managed through the `.env` file. Key settings include:

- **N8N_PORT**: Port to access n8n (default: 5678)
- **N8N_BASIC_AUTH_USER**: Username for basic authentication
- **N8N_BASIC_AUTH_PASSWORD**: Password for basic authentication
- **N8N_ENCRYPTION_KEY**: Encryption key for credentials (required for production)
- **GENERIC_TIMEZONE**: Timezone for n8n (e.g., America/New_York, Europe/London)

See `.env.example` for all available options.

### Using PostgreSQL (Optional)

By default, n8n uses SQLite. For production environments, PostgreSQL is recommended:

1. Uncomment the PostgreSQL service in `docker-compose.yml`
2. Uncomment the database-related environment variables in `.env`
3. Update the `.env` file with your PostgreSQL credentials
4. Restart the containers:
   ```bash
   docker-compose down
   docker-compose up -d
   ```

## Common Commands

### Start n8n
```bash
docker-compose up -d
```

### Stop n8n
```bash
docker-compose down
```

### View logs
```bash
docker-compose logs -f n8n
```

### Restart n8n
```bash
docker-compose restart n8n
```

### Update n8n to latest version
```bash
docker-compose pull
docker-compose up -d
```

### Backup data
```bash
docker-compose exec n8n n8n export:workflow --backup --output=/files/
docker-compose exec n8n n8n export:credentials --backup --output=/files/
```

The backup files will be available in the `./n8n_data` directory.

## Data Persistence

- **n8n data**: Stored in Docker volume `n8n_data` and mapped to `./n8n_data`
- **Database**: If using PostgreSQL, data is stored in the `postgres_data` volume

## Security Considerations

1. **Change default passwords**: Always change `N8N_BASIC_AUTH_PASSWORD` in production
2. **Generate encryption key**: Use `openssl rand -hex 32` to generate a secure encryption key
3. **Use HTTPS**: For production, configure a reverse proxy (nginx/Traefik) with SSL
4. **Firewall**: Ensure only necessary ports are exposed
5. **Keep .env secret**: Never commit `.env` to version control

## Troubleshooting

### Container won't start
Check logs for errors:
```bash
docker-compose logs n8n
```

### Permission issues
Ensure the volumes have correct permissions:
```bash
sudo chown -R 1000:1000 ./n8n_data
```

### Can't access n8n
- Verify the container is running: `docker-compose ps`
- Check if port 5678 is available: `netstat -an | grep 5678`
- Verify firewall settings

## Upgrading

1. Backup your data (see Backup section)
2. Pull the latest image:
   ```bash
   docker-compose pull
   ```
3. Restart containers:
   ```bash
   docker-compose down
   docker-compose up -d
   ```

## Additional Resources

- [n8n Documentation](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [n8n GitHub Repository](https://github.com/n8n-io/n8n)

## License

This Docker setup is provided as-is. n8n itself is licensed under the [Sustainable Use License](https://github.com/n8n-io/n8n/blob/master/LICENSE.md).
