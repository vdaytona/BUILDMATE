# BuildMate — Infrastructure & DevOps Source Code

## Table of Contents
- .gitignore
- docker-compose.yml
- .github/workflows/ci.yml
- .github/workflows/deploy.yml
- server/Dockerfile
- web/Dockerfile
- web/nginx.conf

---

## .gitignore

```gitignore
node_modules/
dist/
.env
*.log
uploads/*
!uploads/.gitkeep
.DS_Store
```

## docker-compose.yml

```yaml
version: '3.8'

services:
  server:
    build:
      context: ./server
      dockerfile: Dockerfile
    container_name: buildmate-server
    restart: unless-stopped
    ports:
      - '3001:3001'
    environment:
      - NODE_ENV=production
      - DATABASE_URL=file:./data/buildmate.db
      - JWT_SECRET=${JWT_SECRET}
      - JWT_REFRESH_SECRET=${JWT_REFRESH_SECRET}
      - CORS_ORIGIN=http://localhost
      - PORT=3001
    volumes:
      - server-uploads:/app/uploads
      - server-data:/app/data
    healthcheck:
      test: ['CMD', 'wget', '-qO-', 'http://localhost:3001/api/health']
      interval: 30s
      timeout: 10s
      retries: 3

  web:
    build:
      context: ./web
      dockerfile: Dockerfile
    container_name: buildmate-web
    restart: unless-stopped
    ports:
      - '80:80'
    depends_on:
      server:
        condition: service_healthy

volumes:
  server-uploads:
  server-data:
```

## .github/workflows/ci.yml

```yaml
name: CI — Lint, Type Check & Test

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  server-typecheck:
    name: Server Type Check
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: server
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
          cache-dependency-path: server/package-lock.json
      - run: npm ci
      - run: npx prisma generate
      - run: npx tsc --noEmit

  web-typecheck:
    name: Web Type Check
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: web
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
          cache-dependency-path: web/package-lock.json
      - run: npm ci
      - run: npx tsc --noEmit

  server-test:
    name: Server Tests
    runs-on: ubuntu-latest
    needs: server-typecheck
    defaults:
      run:
        working-directory: server
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
          cache-dependency-path: server/package-lock.json
      - run: npm ci
      - run: npx prisma generate
      - run: npm test
      - name: Upload coverage
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: server-coverage
          path: server/coverage/
          retention-days: 7

  web-test:
    name: Web Tests
    runs-on: ubuntu-latest
    needs: web-typecheck
    defaults:
      run:
        working-directory: web
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm
          cache-dependency-path: web/package-lock.json
      - run: npm ci
      - run: npm test
      - name: Upload coverage
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: web-coverage
          path: web/coverage/
          retention-days: 7
```

## .github/workflows/deploy.yml

```yaml
name: Build & Deploy

on:
  push:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_PREFIX: ${{ github.repository }}

permissions:
  contents: read
  packages: write

jobs:
  build-server:
    name: Build Server Image
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_PREFIX }}/server
          tags: |
            type=sha,prefix=
            type=raw,value=latest,enable={{is_default_branch}}

      - name: Build and push server image
        uses: docker/build-push-action@v5
        with:
          context: ./server
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}

  build-web:
    name: Build Web Image
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Log in to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_PREFIX }}/web
          tags: |
            type=sha,prefix=
            type=raw,value=latest,enable={{is_default_branch}}

      - name: Build and push web image
        uses: docker/build-push-action@v5
        with:
          context: ./web
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}

  deploy:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    needs: [build-server, build-web]
    environment: staging
    steps:
      - uses: actions/checkout@v4

      - name: Deploy notification
        run: |
          echo "🚀 Images built and pushed to ${{ env.REGISTRY }}"
          echo "Server: ${{ env.REGISTRY }}/${{ env.IMAGE_PREFIX }}/server:latest"
          echo "Web: ${{ env.REGISTRY }}/${{ env.IMAGE_PREFIX }}/web:latest"
          echo ""
          echo "To deploy manually:"
          echo "  docker compose pull && docker compose up -d"
```

## server/Dockerfile

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npx prisma generate
RUN npx tsc

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/prisma ./prisma
COPY package*.json ./
EXPOSE 3001
CMD ["node", "dist/index.js"]
```

## web/Dockerfile

```dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

## web/nginx.conf

```nginx
server {
    listen 80;
    server_name _;

    root /usr/share/nginx/html;
    index index.html;

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml text/javascript image/svg+xml;
    gzip_min_length 256;

    # Cache static assets
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
        try_files $uri =404;
    }

    # SPA routing — serve index.html for all non-file requests
    location / {
        try_files $uri $uri/ /index.html;
    }

    # Proxy API requests to the server
    location /api/ {
        proxy_pass http://server:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # Proxy uploads
    location /uploads/ {
        proxy_pass http://server:3001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
