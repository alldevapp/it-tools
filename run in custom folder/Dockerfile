# Multi-stage build for IT Tools
FROM node:18-alpine AS builder

WORKDIR /app

# Install git and pnpm
RUN apk add --no-cache git
RUN npm install -g pnpm

# Clone the IT Tools repository
RUN git clone https://github.com/CorentinTh/it-tools.git .

# Create a custom vite.config.ts to ensure correct base path
RUN echo "import { defineConfig } from 'vite';" > vite.config.override.ts && \
    echo "import { mergeConfig } from 'vite';" >> vite.config.override.ts && \
    echo "import baseConfig from './vite.config';" >> vite.config.override.ts && \
    echo "export default mergeConfig(baseConfig, defineConfig({" >> vite.config.override.ts && \
    echo "  base: '/v2/'," >> vite.config.override.ts && \
    echo "  build: { assetsDir: 'assets' }" >> vite.config.override.ts && \
    echo "}));" >> vite.config.override.ts

# Install dependencies
RUN pnpm install

# Build with explicit base path
ENV VITE_BASE_URL=/v2/
RUN pnpm vite build --config vite.config.override.ts

# List what was built for debugging
RUN echo "=== Built files ===" && ls -la dist/ && echo "=== Index.html content ===" && head -20 dist/index.html

# Production stage
FROM nginx:alpine

# Install curl for health checks
RUN apk add --no-cache curl

# Copy built application to /v2 path
COPY --from=builder /app/dist /usr/share/nginx/html/v2

# Copy nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port 80
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
