# fullstack9.1
# ==============================
# STAGE 1 — Build React App
# ==============================
FROM node:18-alpine AS build

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy all source files
COPY . .

# Build React app for production
RUN npm run build

# ==============================
# STAGE 2 — Serve with Nginx
# ==============================
FROM nginx:stable-alpine

# Copy React build output from previous stage
COPY --from=build /app/build /usr/share/nginx/html

# Optional: custom Nginx config to support React Router
RUN rm /etc/nginx/conf.d/default.conf
RUN echo 'server { \
    listen 80; \
    server_name localhost; \
    root /usr/share/nginx/html; \
    index index.html index.htm; \
    location / { \
        try_files $uri /index.html; \
    } \
}' > /etc/nginx/conf.d/default.conf

# Expose port 80 for incoming requests
EXPOSE 80

# Start Nginx web server
CMD ["nginx", "-g", "daemon off;"]
