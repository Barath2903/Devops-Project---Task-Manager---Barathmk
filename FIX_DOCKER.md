# Fix Docker Daemon Issue

## Solution: Start Docker Desktop

### On macOS:

1. **Open Docker Desktop:**
   - Press `Cmd + Space` to open Spotlight
   - Type "Docker" and press Enter
   - OR go to Applications → Docker

2. **Wait for Docker to Start:**
   - Look for the Docker whale icon in the menu bar (top right)
   - Wait until it shows "Docker Desktop is running"
   - This usually takes 10-30 seconds

3. **Verify Docker is Running:**
   ```bash
   docker ps
   ```
   If this command works (shows container list or empty), Docker is running!

### Alternative: Start via Terminal

```bash
# Open Docker Desktop
open -a Docker

# Wait a few seconds, then verify
sleep 5
docker ps
```

## After Docker Starts

Once Docker is running, you can start the project:

```bash
cd "/Users/barath/Task Manager Devops"
docker-compose up -d
```

## Troubleshooting

### Docker Desktop Won't Start?

1. **Check if it's already running:**
   ```bash
   ps aux | grep -i docker
   ```

2. **Restart Docker Desktop:**
   - Quit Docker Desktop completely
   - Wait 10 seconds
   - Start it again

3. **Check Docker Desktop Settings:**
   - Open Docker Desktop
   - Go to Settings → General
   - Make sure "Start Docker Desktop when you log in" is enabled

4. **Reinstall Docker Desktop (if needed):**
   - Download from: https://www.docker.com/products/docker-desktop
   - Uninstall old version first
   - Install new version

### Still Having Issues?

```bash
# Check Docker status
docker info

# Check Docker socket
ls -la ~/.docker/run/docker.sock

# Restart Docker service (if using Linux)
sudo systemctl restart docker
```
