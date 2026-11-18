# Docker Command Cheat Sheet: Inventory Tracking Service

**Project:** Inventory Tracking Service Modernization
**Prepared by:** PortNova Solutions for Global Logistics Partners

Every `docker` command below is typed into the **Docker CLI** (the client). The CLI does no real work itself: it sends a REST API request to **`dockerd`** (the Docker Engine daemon), which builds images, runs containers, and talks to Docker Hub.

Run all commands from the repository root (the folder containing `inventory-service/` and `docs/`).

---

## 1. Build the Image

```bash
docker build -t inventory-service:v1 ./inventory-service
```

| Part | Meaning |
|------|---------|
| `docker build` | CLI asks `dockerd` to build an image from a Dockerfile |
| `-t inventory-service:v1` | Tags the image with name `inventory-service` and version `v1` |
| `./inventory-service` | Build context sent to the daemon (must contain the Dockerfile, `package.json`, and source) |

Verify the image exists:

```bash
docker images inventory-service
```

---

## 2. Run the Container (Detached, Port 3000:3000)

```bash
docker run -d -p 3000:3000 --name inventory-service inventory-service:v1
```

| Part | Meaning |
|------|---------|
| `-d` | Detached mode: runs in the background and returns your terminal |
| `-p 3000:3000` | Maps **host** port 3000 to **container** port 3000 (`host:container`) |
| `--name inventory-service` | Gives the container a readable name |
| `inventory-service:v1` | The image to run |

Useful follow-ups:

```bash
docker ps                          # list running containers
docker logs inventory-service      # view application output
docker stop inventory-service      # stop the container
docker rm inventory-service        # remove the stopped container
```

---

## 3. Authenticate, Tag, and Push to Docker Hub

Replace `<dockerhub-username>` with your Docker Hub account or organization name.

```bash
# Step 1: Authenticate (prompts for password or access token)
docker login

# Step 2: Tag the local image with your Docker Hub repository path
docker tag inventory-service:v1 <dockerhub-username>/inventory-service:v1

# Step 3: Push the tagged image to Docker Hub
docker push <dockerhub-username>/inventory-service:v1
```

Optional checks:

```bash
docker images <dockerhub-username>/inventory-service   # confirm the tag
docker logout                                          # end the session on shared machines
```

> **Tip:** Prefer a Docker Hub **access token** over your account password when running `docker login`.
