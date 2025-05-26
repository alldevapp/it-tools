# Multi-stage build for IT Tools
FROM node:18-alpine AS builder

WORKDIR /app

# Install git and pnpm
RUN apk add --no-cache git
RUN npm install -g pnpm

# Clone the IT Tools repository
RUN git clone https://github.com/CorentinTh/it-tools.git .

# Install dependencies
RUN pnpm install

# Build with custom base path - using Vite's base option
RUN pnpm run build --base=/devtools/

# Production stage
FROM nginx:alpine

# Copy built application to /devtools path
COPY --from=builder /app/dist /usr/share/nginx/html/devtools

# Copy nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port 80
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
