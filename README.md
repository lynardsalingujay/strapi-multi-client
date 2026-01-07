# Strapi Multi-Client Docker Setup

Isolated Strapi instances for multiple clients using Docker Compose.

## 🏗️ Architecture

Each client has their own:
- Strapi instance
- PostgreSQL database
- Isolated network
- Persistent storage

## 📁 Project Structure
```
.
├── bayanbites/
│   ├── .env                    # Client secrets (not in git)
│   ├── docker-compose.yml      # Docker configuration
│   ├── postgres-data/          # Database data (auto-created)
│   ├── uploads/               # Media files (auto-created)
│   ├── src/                   # Schema definitions (auto-created)
│   └── database/              # Migrations (auto-created)
├── yumyum/
│   └── ... (same structure)
├── edgardo/
│   └── ... (same structure)
├── .env.example               # Environment template
├── .gitignore
└── README.md
```

## 🚀 Quick Start

### 1. Clone the Repository
```bash
git clone <your-repo-url>
cd <your-repo-name>
```

### 2. Setup a Client
```bash
# Navigate to client folder
cd bayanbites

# Copy environment template
cp ../.env.example .env

# Edit .env with your secrets
nano .env
```

### 3. Generate Secrets

Generate secure secrets for each client:
```bash
# Generate each secret (run 5 times for 5 secrets)
openssl rand -base64 32
```

### 4. Start the Stack
```bash
# Development mode
docker-compose up -d

# Check logs
docker logs bayanbites-strapi -f

# Access Strapi admin
# http://localhost:1340/admin
```

## 🔄 Switching Modes

### Development Mode
- Schema changes allowed
- Content-Type Builder accessible
- Auto-reload enabled
```bash
# In .env
NODE_ENV=development

# Restart
docker-compose down && docker-compose up -d
```

### Production Mode
- Schema changes disabled
- Optimized performance
- Better security
```bash
# In .env
NODE_ENV=production

# Optional: Make src read-only in docker-compose.yml
# - ./src:/app/src:ro

# Restart
docker-compose down && docker-compose up -d
```

## 📝 Managing Multiple Clients

### Port Mapping
- **bayanbites**: `1340`
- **yumyum**: `1341`
- **edgardo**: `1342`

Change `STRAPI_PORT` in each client's `.env` file.

### Adding a New Client
```bash
# 1. Copy existing client folder
cp -r bayanbites newclient

# 2. Edit newclient/.env
cd newclient
nano .env
# - Change STRAPI_PORT (e.g., 1343)
# - Generate new secrets

# 3. Edit docker-compose.yml
# - Update container names (newclient-postgres, newclient-strapi)
# - Update network name (newclient-net)

# 4. Start
docker-compose up -d
```

## 🔒 Security

### CRITICAL: Never Commit
- `.env` files
- `postgres-data/` folder
- Any folder containing secrets

### Best Practices
1. Use strong, unique secrets per client
2. Backup `.env` files securely (outside git)
3. Use environment-specific configurations
4. Enable firewall rules for production

## 🛠️ Common Commands
```bash
# Start services
docker-compose up -d

# Stop services (keeps data)
docker-compose stop

# Stop and remove containers (keeps volumes)
docker-compose down

# View logs
docker logs bayanbites-strapi -f

# Access Strapi container
docker exec -it bayanbites-strapi sh

# Rebuild Strapi image
docker build -t strapi4-base:v1.0.0 .

# Check running containers
docker ps
```

## 💾 Backup Strategy

### What to Backup
1. **Database**: `postgres-data/` folder
2. **Uploads**: `uploads/` folder
3. **Schemas**: `src/` folder
4. **Environment**: `.env` file (securely)

### Backup Command
```bash
# Create backup
tar -czf bayanbites-backup-$(date +%Y%m%d).tar.gz \
  postgres-data/ uploads/ src/ database/ .env

# Restore backup
tar -xzf bayanbites-backup-20240107.tar.gz
```

## 🐛 Troubleshooting

### Container won't start
```bash
# Check logs
docker logs bayanbites-strapi

# Check if port is in use
netstat -tuln | grep 1340

# Remove and recreate
docker-compose down -v
docker-compose up -d
```

### Schema changes not persisting
- Ensure `./src:/app/src` volume is mounted
- Check folder permissions
- Verify you're in development mode

### Database connection failed
- Check `.env` credentials match
- Verify postgres container is healthy: `docker ps`
- Check network: `docker network ls`

## 📚 Resources

- [Strapi Documentation](https://docs.strapi.io)
- [Docker Compose Docs](https://docs.docker.com/compose/)
- [PostgreSQL Docker Hub](https://hub.docker.com/_/postgres)

## 🏷️ Versioning

- **Strapi Base Image**: `strapi4-base:v1.0.0`
- **PostgreSQL**: `15`
- **Node.js**: `18-bullseye`

## 📄 License

[Your License Here]
