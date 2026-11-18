# syntax=docker/dockerfile:1
# =============================================================================
# Inventory Tracking Service - baseline container image
# Global Logistics Partners | Prepared by PortNova Solutions
#
# Assumptions about the build context (the folder passed to `docker build`):
#   - package.json (and package-lock.json, if present) at the root
#   - application entry point at ./server.js, listening on port 3000
#   - a .dockerignore listing node_modules, .git, *.log and .env files
# =============================================================================

# FROM: sets the base image every later instruction builds on. The official
# Node.js "alpine" variant is used because Alpine Linux is tiny (a few MB), so
# the image stays small and ships far fewer OS packages to patch or exploit.
# The major version (24) is pinned so every build gets the same runtime.
FROM node:24-alpine

# ENV: sets an environment variable for the build and for the running
# container. NODE_ENV=production makes npm skip dev tooling and tells
# libraries such as Express to use their optimized production behavior.
ENV NODE_ENV=production

# WORKDIR: creates /app and makes it the working directory for all following
# instructions and for the running container, so we never copy files into the
# filesystem root.
WORKDIR /app

# COPY (dependency manifests only): copies package.json (and the lock file via
# the wildcard) before any source code. Docker caches each layer, so the
# expensive install step below is only re-run when dependencies change, not on
# every source-code edit.
COPY package*.json ./

# RUN: executes a command at build time and bakes the result into the image.
# Installs production dependencies only (--omit=dev), then clears npm's cache
# in the same layer so the cache never adds to the image size.
RUN npm install --omit=dev && npm cache clean --force

# COPY (application source): copies the rest of the build context into /app.
# This comes after the install so code changes reuse the cached dependency
# layer. --chown gives the unprivileged "node" user ownership of the files.
COPY --chown=node:node . .

# USER: switches from root to "node", an unprivileged user that already exists
# in official Node images. Everything after this line, including the running
# application, executes without root, limiting the damage of a compromise.
USER node

# EXPOSE: documents that the service listens on TCP port 3000. It does not
# publish the port; that happens at run time with `docker run -p 3000:3000`.
EXPOSE 3000

# CMD: the default command executed when a container starts. Exec form (JSON
# array) runs node directly as PID 1 so it receives stop signals (SIGTERM)
# properly instead of being wrapped in a shell.
CMD ["node", "server.js"]
