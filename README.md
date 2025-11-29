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

Create a `.env` file based on the provided `env_example` template:

**On Linux/macOS:**

```bash
cp env_example .env
```

**On Windows (PowerShell):**

```bash
Copy-Item env_example .env
```

**On Windows (Command Prompt):**

```cmd
copy env_example .env
```

Then edit the `.env` file with your preferred text editor to customize the configuration if needed.

### Configuration Options

All configuration options are set in the `.env` file. Here are the available options:

#### Open WebUI Configuration

- **`OPENWEBUI_PORT`** (default: `3000`)
  - The port on your host machine to access the Open WebUI interface
  - Example: Set to `8080` to access Open WebUI at `http://localhost:8080`
  - **Note**: The container always runs on port `8080` internally; this setting only changes the host port mapping

#### Ollama Configuration

- **`OLLAMA_PORT`** (default: `11434`)
  - The port for the Ollama API service
  - Used for both host-to-container port mapping and service communication
  - **Note**: If changed, ensure `OLLAMA_API_BASE_URL` (if manually set) matches this port

- **`OLLAMA_SERVICE_NAME`** (default: `ollama`)
  - The Docker service name for Ollama (used for internal service discovery)
  - Typically you won't need to change this unless you modify the service name in `docker-compose.yml`

- **`OLLAMA_MODEL`** (default: `gemma3`)
  - The model to automatically pull and load when containers start
  - Examples: `llama3`, `llama3.2`, `gemma3`, `mistral`, `codellama`, etc.
  - **Note**: The first startup will download the model, which may take several minutes depending on model size and internet speed
  - See [Ollama Model Library](https://ollama.ai/library) for available models

- **`OLLAMA_API_BASE_URL`** (optional, auto-generated)
  - The full URL for the Ollama API endpoint
  - **Auto-generated** from `OLLAMA_SERVICE_NAME` and `OLLAMA_PORT` as: `http://<OLLAMA_SERVICE_NAME>:<OLLAMA_PORT>`
  - Default auto-generated value: `http://ollama:11434`
  - Only override if you need a custom URL (e.g., external Ollama instance)
  - Format: `http://hostname:port` or `https://hostname:port`

#### Example `.env` Configuration

```bash
# Open WebUI Configuration
OPENWEBUI_PORT=3000

# Ollama Configuration
OLLAMA_PORT=11434
OLLAMA_SERVICE_NAME=ollama
OLLAMA_MODEL=gemma3

# Optional: Override auto-generated API URL
# OLLAMA_API_BASE_URL=http://ollama:11434
```

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

(Replace `11434` with your configured `OLLAMA_PORT` if you changed it)

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

### Updating Containers

To update your containers to the latest versions:

**Update all containers:**

```bash
# Pull the latest images
docker compose pull

# Recreate containers with new images (preserves data)
docker compose up -d
```

**Update a specific service:**

```bash
# Pull latest image for a specific service
docker compose pull open-webui
# or
docker compose pull ollama

# Recreate that specific service
docker compose up -d open-webui
# or
docker compose up -d ollama
```

**Update and rebuild (if docker-compose.yml changed):**

```bash
# Pull latest images and recreate all containers
docker compose pull
docker compose up -d --force-recreate
```

**Best Practices:**

1. **Backup your data** before updating (especially if using custom models):

   ```bash
   # Backup Ollama models
   cp -r ./volumes ./volumes.backup
   ```

2. **Check for breaking changes** in the service release notes before updating

3. **Verify after update**: Check that services are running correctly:

   ```bash
   docker compose ps
   docker compose logs
   ```

4. **Pull specific image tags** (if needed):
   - Open WebUI: Uses `main` tag (latest stable)
   - Ollama: Uses `latest` tag

**Note**: Updates will preserve your data volumes, but model downloads may be re-validated on first startup after an Ollama update.

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
