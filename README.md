# Ollama Repository

Docker Compose setup for running Ollama with Open WebUI interface.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- **Docker** (version 20.10 or later)
  - [Download Docker Desktop](https://www.docker.com/products/docker-desktop/)
  
- **Docker Compose** (version 2.0 or later)
  - Usually included with Docker Desktop
  - Verify installation: `docker compose version`

## Installation

### 1. Clone or Download the Repository

If you have git installed:

```bash
git clone <repository-url>
cd mlk_docker_ollama
```

Or download and extract the ZIP file to a directory of your choice.

### 2. Configure Environment Variables

Create a `.env` file in the project root directory (if it doesn't already exist):

```bash
# Open WebUI Configuration
OPENWEBUI_PORT=3000

# Ollama API Configuration (optional - uses default if not set)
OLLAMA_API_BASE_URL=http://ollama:11434
```

**Configuration Options:**

- `OPENWEBUI_PORT`: The port on your host machine to access Open WebUI (default: 3000)
- `OLLAMA_API_BASE_URL`: The Ollama API URL (default: <http://ollama:11434>)

### 3. Start the Services

Navigate to the project directory and start the containers:

```bash
docker compose up -d
```

The `-d` flag runs the containers in detached mode (in the background).

### 4. Verify Installation

Check that the containers are running:

```bash
docker compose ps
```

You should see both `open-webui` and `ollama` services running.

## Usage

### Accessing Open WebUI

Once the containers are running, open your web browser and navigate to:

```text
http://localhost:3000
```

(Replace `3000` with your configured `OPENWEBUI_PORT` if you changed it)

### Accessing Ollama API

The Ollama API is available at:

```text
http://localhost:11434
```

### Managing Services

**Stop the services:**

```bash
docker compose down
```

**Stop and remove volumes (WARNING: This will delete all data):**

```bash
docker compose down -v
```

**View logs:**

```bash
# View all logs
docker compose logs

# View logs for a specific service
docker compose logs open-webui
docker compose logs ollama

# Follow logs in real-time
docker compose logs -f
```

**Restart a specific service:**

```bash
docker compose restart open-webui
docker compose restart ollama
```

## Data Persistence

- **Ollama models**: Stored in `./volumes` directory (mapped to `/root/.ollama` in the container)
- **Open WebUI data**: Stored in a Docker volume named `open-webui-data`

## Troubleshooting

### Port Already in Use

If you get an error that port 3000 (or your configured port) is already in use:

1. Change the `OPENWEBUI_PORT` value in your `.env` file
2. Restart the containers: `docker compose down && docker compose up -d`

### Container Won't Start

1. Check the logs: `docker compose logs`
2. Ensure Docker is running: `docker ps`
3. Verify your `.env` file has correct formatting (no spaces around `=`)
4. Check available disk space: `docker system df`

### Reset Everything

To start fresh (removes all containers, volumes, and data):

```bash
docker compose down -v
docker compose up -d
```

## Additional Resources

- [Ollama Documentation](https://ollama.ai/docs)
- [Open WebUI Documentation](https://docs.openwebui.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
