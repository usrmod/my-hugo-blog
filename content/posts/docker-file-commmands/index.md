
---
title: "Docker File Commmands"  # Hugo archetype templating
date: "2026-02-13T03:15:46+05:30"
draft: false
tags: ["docker", "syntax"]
categories: ["tutorials"]
showToc: true
TocOpen: false
hidemeta: false
comments: false
description: "All important components of Dockerfile syntax in one place"
summary: "Breakdown of Dockerfile syntax"
canonicalURL: ""         # Example: "https://canonical.url/to/page"
disableShare: false
hideSummary: false      # true = hide the summary/excerpt on list pages
searchHidden: false     # true = hide from search index (if you enabled search)
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
UseHugoToc: true
cover:
  image: ""              # e.g. "cover.png" (with relative:true) or "/img/cover.png" or "https://..."
  alt: ""                # alt text for accessibility
  caption: ""            # caption under the cover image
  relative: true         # true for page bundles; false for absolute/URL paths
  hidden: false          # true = hide cover only on single post page
editPost:
  URL: ""                # e.g. "https://github.com/<user>/<repo>/blob/main/content"
  Text: "Suggest Changes"
  appendFilePath: true
---
<!--more-->
> TLDR;
- Dockerfile syntax explained with examples
- To the point no fluff article
---
## 1. Dockerfile Fundamentals

### 1.1 What Is a Dockerfile?

A **Dockerfile is a text file with instructions** to build a Docker image. [docs.docker](https://docs.docker.com/get-started/docker-concepts/building-images/multi-stage-builds/)

**Each instruction creates a layer** in the final image.

 **Written in a specific DSL** (Domain Specific Language) with commands in UPPERCASE.

```
Dockerfile → docker build → Docker Image → docker run → Container

Text file    Build process   Template        Run process   Running app
```

### 1.2 Basic Structure

```dockerfile
# Comments start with #
INSTRUCTION arguments

# Example:
FROM python:3.13
RUN apt-get update
COPY app.py /app/
CMD ["python", "/app/app.py"]
```

***

## 2. Dockerfile Instructions Reference

### 2.1 FROM - Base Image

**Syntax:**
```dockerfile
FROM <image>[:<tag>] [AS <name>]
```

**What it does:**
- Specifies the base image to start from
- **Must be the first instruction** (except ARG before FROM)
- Every Dockerfile starts with FROM

**Examples:**
```dockerfile
# Use official Python image
FROM python:3.13

# Use slim variant
FROM python:3.13-slim

# Use Alpine Linux (smallest)
FROM python:3.13-alpine

# Use specific version
FROM python:3.13.1

# Named stage for multi-stage builds
FROM python:3.13 AS builder

# Use Ubuntu base
FROM ubuntu:22.04
```

**Common base images:**
```
python:3.13           → 1.2GB (includes everything)
python:3.13-slim      → 150MB (minimal Debian)
python:3.13-alpine    → 50MB  (tiny but compatibility issues)
ubuntu:22.04          → 77MB  (general purpose)
debian:bookworm-slim  → 74MB  (Debian minimal)
```

***

### 2.2 RUN - Execute Commands

**Syntax:**
```dockerfile
# Shell form (runs in /bin/sh -c)
RUN <command>

# Exec form (runs directly)
RUN ["executable", "param1", "param2"]
```

**What it does:**
- Executes commands **during image build**
- Creates a new layer
- Used to install packages, configure environment

**Examples:**
```dockerfile
# Install packages (shell form)
RUN apt-get update && apt-get install -y curl

# Multiple commands with line continuation
RUN apt-get update && \
    apt-get install -y \
        curl \
        vim \
        git && \
    rm -rf /var/lib/apt/lists/*

# Run Python script
RUN python setup.py install

# Exec form (more explicit)
RUN ["/bin/bash", "-c", "echo hello"]
```

**Best practices:**
```dockerfile
# ❌ BAD: Each RUN creates a new layer
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y vim

# ✅ GOOD: Combine into single layer
RUN apt-get update && \
    apt-get install -y curl vim && \
    rm -rf /var/lib/apt/lists/*
```

***

### 2.3 COPY - Copy Files

**Syntax:**
```dockerfile
COPY [--chown=<user>:<group>] <src>... <dest>
COPY [--chown=<user>:<group>] ["<src>",... "<dest>"]
```

**What it does:**
- Copies files from **build context** (local machine) to image
- Simple, predictable file copying

**Examples:**
```dockerfile
# Copy single file
COPY app.py /app/app.py

# Copy to directory (creates if doesn't exist)
COPY app.py /app/

# Copy multiple files
COPY app.py config.py /app/

# Copy entire directory
COPY src/ /app/src/

# Copy everything (respects .dockerignore)
COPY . /app/

# Copy with ownership
COPY --chown=user:group file.txt /app/

# Copy multiple sources
COPY file1.txt file2.txt /app/
```

**Wildcards:**
```dockerfile
# Copy all Python files
COPY *.py /app/

# Copy files matching pattern
COPY requirements*.txt /app/
```

***

### 2.4 ADD - Advanced Copy

**Syntax:**
```dockerfile
ADD <src>... <dest>
```

**What it does:**
- Like COPY but with extra features:
  - Auto-extracts tar archives
  - Can download from URLs

**Examples:**
```dockerfile
# Extract tar file automatically
ADD archive.tar.gz /app/

# Download from URL (not recommended)
ADD https://example.com/file.tar.gz /tmp/

# Regular file copy (same as COPY)
ADD app.py /app/
```

**When to use:**
```dockerfile
# ✅ Use ADD for: Auto-extracting archives
ADD package.tar.gz /opt/

# ❌ Use COPY for: Everything else
COPY app.py /app/
```

***

### 2.5 WORKDIR - Set Working Directory

**Syntax:**
```dockerfile
WORKDIR /path/to/directory
```

**What it does:**
- Sets the working directory for subsequent instructions
- Creates directory if it doesn't exist
- Like `cd` in shell but persistent

**Examples:**
```dockerfile
# Set working directory
WORKDIR /app

# All subsequent commands run in /app
COPY . .          # Copies to /app/
RUN ls            # Lists /app/

# Nested directories
WORKDIR /app/src/modules
# Now in /app/src/modules

# Relative paths
WORKDIR /app
WORKDIR src       # Now in /app/src
```

**Best practice:**
```dockerfile
# ❌ BAD: Using cd in RUN
RUN cd /app && python setup.py install

# ✅ GOOD: Using WORKDIR
WORKDIR /app
RUN python setup.py install
```

***

### 2.6 ENV - Environment Variables

**Syntax:**
```dockerfile
ENV <key>=<value> ...
ENV <key> <value>
```

**What it does:**
- Sets environment variables
- Available during build AND runtime
- Can be overridden at runtime

**Examples:**
```dockerfile
# Single variable
ENV APP_VERSION=1.0.0

# Multiple variables
ENV APP_ENV=production \
    DEBUG=false \
    PORT=8000

# Old syntax (deprecated)
ENV APP_NAME myapp

# Using in subsequent commands
ENV DATA_DIR=/data
WORKDIR $DATA_DIR
```

**Common patterns:**
```dockerfile
# Python environment
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# Path modification
ENV PATH="/app/.venv/bin:$PATH"

# Application config
ENV DATABASE_URL=postgresql://localhost/db \
    REDIS_URL=redis://localhost:6379
```

***

### 2.7 ARG - Build Arguments

**Syntax:**
```dockerfile
ARG <name>[=<default value>]
```

**What it does:**
- Defines build-time variables
- **Only available during build**, not at runtime
- Can be passed with `docker build --build-arg`

**Examples:**
```dockerfile
# Define with default
ARG PYTHON_VERSION=3.13

# Use in FROM
FROM python:${PYTHON_VERSION}

# Define without default
ARG BUILD_ENV

# Use in RUN
RUN echo "Building for ${BUILD_ENV}"

# Multiple args
ARG USER_ID=1000
ARG USER_NAME=appuser
RUN useradd -u ${USER_ID} ${USER_NAME}
```

**Usage:**
```bash
# Build with custom argument
docker build --build-arg PYTHON_VERSION=3.12 -t myapp .
docker build --build-arg BUILD_ENV=staging -t myapp .
```

**ARG vs ENV:**
```dockerfile
# ARG: Build-time only
ARG BUILD_DATE
RUN echo "Built on ${BUILD_DATE}" > /build-info.txt

# ENV: Build + Runtime
ENV APP_VERSION=1.0
RUN echo "Version ${APP_VERSION}"  # Available
CMD echo "Version ${APP_VERSION}"  # Available

# ARG not available at runtime
ARG TEMP_VAR
CMD echo "${TEMP_VAR}"  # Empty!
```

***

### 2.8 CMD - Default Command

**Syntax:**
```dockerfile
# Exec form (preferred)
CMD ["executable", "param1", "param2"]

# Shell form
CMD command param1 param2

# As parameters to ENTRYPOINT
CMD ["param1", "param2"]
```

**What it does:**
- Specifies **default command** to run when container starts
- Only **one CMD** per Dockerfile (last one wins)
- Can be overridden at runtime

**Examples:**
```dockerfile
# Run Python script (exec form)
CMD ["python", "app.py"]

# Run with arguments
CMD ["python", "app.py", "--port", "8000"]

# Shell form (runs in /bin/sh -c)
CMD python app.py

# Interactive shell
CMD ["/bin/bash"]
```

**Override at runtime:**
```bash
# Dockerfile has: CMD ["python", "app.py"]

# Use default
docker run myapp
# Runs: python app.py

# Override
docker run myapp python test.py
# Runs: python test.py
```

***

### 2.9 ENTRYPOINT - Fixed Command

**Syntax:**
```dockerfile
# Exec form (preferred)
ENTRYPOINT ["executable", "param1", "param2"]

# Shell form
ENTRYPOINT command param1 param2
```

**What it does:**
- Configures container to run as an **executable**
- **Cannot be easily overridden** (more fixed than CMD)
- Often used with CMD for default arguments

**Examples:**
```dockerfile
# Fixed command
ENTRYPOINT ["python", "app.py"]

# Container always runs Python script
# docker run myapp → python app.py
# docker run myapp --help → python app.py --help

# With CMD for defaults
ENTRYPOINT ["python", "app.py"]
CMD ["--port", "8000"]
# docker run myapp → python app.py --port 8000
# docker run myapp --port 9000 → python app.py --port 9000
```

**ENTRYPOINT vs CMD:**
```dockerfile
# Pattern 1: CMD only (fully replaceable)
CMD ["python", "app.py"]
# docker run myapp → python app.py
# docker run myapp bash → bash

# Pattern 2: ENTRYPOINT only (fixed command)
ENTRYPOINT ["python", "app.py"]
# docker run myapp → python app.py
# docker run myapp bash → python app.py bash (wrong!)

# Pattern 3: Both (best flexibility)
ENTRYPOINT ["python"]
CMD ["app.py"]
# docker run myapp → python app.py
# docker run myapp test.py → python test.py
```

***

### 2.10 EXPOSE - Document Ports

**Syntax:**
```dockerfile
EXPOSE <port> [<port>/<protocol>...]
```

**What it does:**
- **Documents** which ports the application listens on
- **Does NOT actually publish** the port
- Metadata only

**Examples:**
```dockerfile
# Single port
EXPOSE 8000

# Multiple ports
EXPOSE 8000 8080

# With protocol
EXPOSE 80/tcp
EXPOSE 53/udp

# Range
EXPOSE 8000-8010
```

**Usage:**
```bash
# Dockerfile has: EXPOSE 8000

# Still need -p to publish
docker run -p 8000:8000 myapp  # Maps port

# EXPOSE just documents the intent
```

***

### 2.11 VOLUME - Mount Points

**Syntax:**
```dockerfile
VOLUME ["/path/to/volume"]
VOLUME /path/to/volume
```

**What it does:**
- Declares a **mount point** for external volumes
- Data persists outside container
- Can be overridden at runtime

**Examples:**
```dockerfile
# Single volume
VOLUME /data

# Multiple volumes
VOLUME ["/data", "/logs"]

# Application example
VOLUME /app/uploads
VOLUME /var/log/myapp
```

**Usage:**
```bash
# Dockerfile has: VOLUME /data

# Docker creates anonymous volume
docker run myapp
# Data in /data persists in Docker volume

# Mount specific directory
docker run -v /host/data:/data myapp
```

***

### 2.12 USER - Change User

**Syntax:**
```dockerfile
USER <user>[:<group>]
USER <UID>[:<GID>]
```

**What it does:**
- Sets the user for subsequent commands
- Security best practice (don't run as root)

**Examples:**
```dockerfile
# Create user first
RUN useradd -m appuser

# Switch to user
USER appuser

# All subsequent commands run as appuser
RUN whoami  # Returns: appuser

# With group
USER appuser:appgroup

# Using UID:GID
USER 1000:1000
```

**Complete pattern:**
```dockerfile
# Create non-root user
RUN groupadd -g 1001 appuser && \
    useradd -u 1001 -g appuser -m -d /app appuser

# Set ownership
COPY --chown=appuser:appuser . /app/

# Switch to user
USER appuser

# Application runs as appuser
CMD ["python", "app.py"]
```

***

### 2.13 LABEL - Metadata

**Syntax:**
```dockerfile
LABEL <key>=<value> <key>=<value> ...
```

**What it does:**
- Adds metadata to image
- Used for documentation, version tracking

**Examples:**
```dockerfile
# Single label
LABEL version="1.0"

# Multiple labels
LABEL maintainer="you@example.com" \
      description="My application" \
      version="1.0.0"

# Organization labels
LABEL org.opencontainers.image.authors="your-name" \
      org.opencontainers.image.version="1.0.0" \
      org.opencontainers.image.created="2026-02-13"
```

***

### 2.14 HEALTHCHECK - Container Health

**Syntax:**
```dockerfile
HEALTHCHECK [OPTIONS] CMD command
HEALTHCHECK NONE  # Disable health check
```

**What it does:**
- Tells Docker how to test if container is healthy
- Runs periodically

**Options:**
```dockerfile
--interval=DURATION (default: 30s)
--timeout=DURATION (default: 30s)
--start-period=DURATION (default: 0s)
--retries=N (default: 3)
```

**Examples:**
```dockerfile
# Simple check
HEALTHCHECK CMD curl -f http://localhost/ || exit 1

# Full options
HEALTHCHECK --interval=30s \
            --timeout=10s \
            --start-period=5s \
            --retries=3 \
  CMD curl -f http://localhost:8000/health || exit 1

# Python script check
HEALTHCHECK CMD python -c "import requests; requests.get('http://localhost:8000/health')"

# Disable inherited health check
HEALTHCHECK NONE
```

***

## 3. Complete Dockerfile Example (Annotated)

```dockerfile
# ============================================
# Stage 1: Builder
# ============================================

# 1. Start from base image with name
FROM python:3.13-slim AS builder

# 2. Set build argument with default
ARG PYTHON_VERSION=3.13

# 3. Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# 4. Add metadata
LABEL maintainer="you@example.com" \
      version="1.0.0"

# 5. Set working directory
WORKDIR /app

# 6. Install system dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        gcc \
        curl && \
    rm -rf /var/lib/apt/lists/*

# 7. Copy requirements first (for caching)
COPY requirements.txt .

# 8. Install Python packages
RUN pip install --no-cache-dir -r requirements.txt

# 9. Copy application code
COPY . .

# ============================================
# Stage 2: Runtime
# ============================================

# 10. New stage with smaller base
FROM python:3.13-slim

# 11. Set environment for runtime
ENV PYTHONUNBUFFERED=1 \
    PATH="/app/.venv/bin:$PATH"

# 12. Create non-root user
RUN groupadd -g 1001 appuser && \
    useradd -u 1001 -g appuser -m -d /app appuser

# 13. Set working directory
WORKDIR /app

# 14. Copy from builder stage
COPY --from=builder --chown=appuser:appuser /app /app

# 15. Create volume mount point
VOLUME /app/data

# 16. Switch to non-root user
USER appuser

# 17. Document port
EXPOSE 8000

# 18. Add health check
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD curl -f http://localhost:8000/health || exit 1

# 19. Set default command
ENTRYPOINT ["python"]
CMD ["app.py"]
```

***

## 4. Dockerfile Syntax Rules

### 4.1 General Rules

```dockerfile
# 1. Instructions are CASE-INSENSITIVE but UPPERCASE is convention
from python:3.13  # Works but discouraged
FROM python:3.13  # Preferred

# 2. Comments start with #
# This is a comment
FROM python:3.13

# 3. Line continuation with backslash
RUN apt-get update && \
    apt-get install -y curl

# 4. Parser directives (must be first line)
# syntax=docker/dockerfile:1
FROM python:3.13

# 5. Whitespace doesn't matter (mostly)
FROM python:3.13
FROM   python:3.13   # Same
```

### 4.2 Shell vs Exec Form

```dockerfile
# Shell form: Runs in /bin/sh -c
RUN apt-get update
CMD python app.py
ENTRYPOINT python app.py

# Exec form: Direct execution (no shell)
RUN ["apt-get", "update"]
CMD ["python", "app.py"]
ENTRYPOINT ["python", "app.py"]
```

**Differences:**
```dockerfile
# Shell form: Variable substitution works
ENV NAME=world
RUN echo "Hello $NAME"  # Prints: Hello world

# Exec form: No variable substitution
ENV NAME=world
RUN ["echo", "Hello $NAME"]  # Prints: Hello $NAME (literal)

# Exec form with shell for variables
RUN ["/bin/sh", "-c", "echo Hello $NAME"]  # Prints: Hello world
```

***

## 5. .dockerignore File

### 5.1 What Is It?

```
Like .gitignore but for Docker builds
Excludes files from build context
Speeds up builds, reduces image size
```

### 5.2 Syntax

```
# .dockerignore

# Comments start with #
*.log

# Wildcard patterns
*.tmp
temp*

# Directories
node_modules/
.git/

# Exceptions (include something excluded)
!important.log

# Match any directory
**/*.pyc
```

### 5.3 Common Python .dockerignore

```
# .dockerignore for Python projects
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
dist/
*.egg-info/
.venv/
venv/
.env
.git/
.gitignore
.vscode/
.idea/
*.md
tests/
.pytest_cache/
.coverage
htmlcov/
```

***

## 6. Build Context Explained

### 6.1 What Is Build Context?

```
When you run: docker build -t myapp .
                                    ^
                                    |
                        This is the build context

Build context = Directory sent to Docker daemon
               = What COPY/ADD can access
```

### 6.2 Context Structure

```
project/
├── Dockerfile         ← Build instructions
├── .dockerignore      ← Exclusions
├── app.py             ← Included in context
├── requirements.txt   ← Included
├── .git/              ← Excluded (via .dockerignore)
└── node_modules/      ← Excluded (via .dockerignore)

Build context = Everything except .dockerignore matches
```

***

## 7. Practice Exercises

### 7.1 Exercise 1: Simple Python App

**Task:** Create a Dockerfile for this app:
```python
# app.py
print("Hello from Docker!")
```

**Solution:**
```dockerfile
FROM python:3.13-slim
WORKDIR /app
COPY app.py .
CMD ["python", "app.py"]
```

### 7.2 Exercise 2: Flask Web App

**Task:** Dockerize a Flask app with requirements:
```
# requirements.txt
flask==3.0.0
```

**Solution:**
```dockerfile
FROM python:3.13-slim

WORKDIR /app

# Copy requirements first
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy app
COPY app.py .

# Document port
EXPOSE 5000

# Run Flask
CMD ["python", "-m", "flask", "run", "--host=0.0.0.0"]
```

### 7.3 Exercise 3: Multi-Stage Build

**Task:** Create a multi-stage build that:
1. Builds in stage 1
2. Runs in smaller stage 2

**Solution:**
```dockerfile
# Stage 1: Build
FROM python:3.13 AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Stage 2: Runtime
FROM python:3.13-slim
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.13/site-packages /usr/local/lib/python3.13/site-packages
COPY app.py .
CMD ["python", "app.py"]
```

***

## 8. Common Patterns

### 8.1 Python Web App Pattern

```dockerfile
FROM python:3.13-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "app:app"]
```

### 8.2 Development vs Production

```dockerfile
# Use ARG to switch
ARG BUILD_ENV=production

FROM python:3.13-slim

WORKDIR /app

COPY requirements.txt .

# Install different dependencies based on environment
RUN if [ "$BUILD_ENV" = "development" ]; then \
        pip install -r requirements-dev.txt; \
    else \
        pip install -r requirements.txt; \
    fi

COPY . .

CMD ["python", "app.py"]
```

### 8.3 With Environment File

```dockerfile
FROM python:3.13-slim
WORKDIR /app

# Copy and source .env
COPY .env .
RUN export $(cat .env | xargs)

COPY . .
CMD ["python", "app.py"]
```

***

## 9. Debugging Dockerfile

### 9.1 Build with Output

```bash
# Verbose build
docker build --progress=plain -t myapp .

# No cache (rebuild everything)
docker build --no-cache -t myapp .

# Stop at specific stage
docker build --target builder -t myapp:builder .
```

### 9.2 Inspect Layers

```bash
# Show image layers
docker history myapp

# Inspect image
docker inspect myapp

# Dive into image structure
dive myapp  # Install dive tool first
```

### 9.3 Debug Failed Build

```bash
# Build fails at RUN step
# Docker keeps intermediate container
# Find its ID in error output

# Run the failed container
docker run -it <container_id> /bin/bash

# Now you can debug interactively
```

***

## 10. Quick Reference Card

```dockerfile
# ===== BASICS =====
FROM image:tag              # Base image
RUN command                 # Execute during build
COPY src dest               # Copy files
WORKDIR /path               # Set directory

# ===== CONFIGURATION =====
ENV KEY=value               # Environment variable
ARG KEY=value               # Build argument
EXPOSE port                 # Document port
VOLUME /path                # Mount point

# ===== EXECUTION =====
CMD ["exec", "args"]        # Default command
ENTRYPOINT ["exec"]         # Fixed command
USER username               # Switch user

# ===== METADATA =====
LABEL key=value             # Add metadata
HEALTHCHECK CMD command     # Health check

# ===== MULTI-STAGE =====
FROM base AS name           # Named stage
COPY --from=name src dest   # Copy from stage
```