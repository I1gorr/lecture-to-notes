# ---- Build Stage ----
FROM oven/bun:1.3.1 AS build

# Set working directory inside container
WORKDIR /frontend

# Copy dependency manifests first (for caching)
COPY package*.json ./

# Install dependencies (skip postinstall to avoid esbuild check issue)
RUN bun install --ignore-scripts

# Copy the rest of the frontend source code
COPY . .

# Build static production files (Vite output)
RUN bun run build

# ---- Serve Stage ----
FROM nginx:alpine

# Copy compiled output from build stage to nginx html folder
COPY --from=build /frontend/dist /usr/share/nginx/html

# Optional: Custom nginx config for single-page apps (React/Vite)
# Ensures client-side routing works
RUN rm /etc/nginx/conf.d/default.conf
COPY <<EOF /etc/nginx/conf.d/default.conf
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;
    location / {
        try_files \$uri /index.html;
    }
}
EOF

# Expose port 80
EXPOSE 80

# Run nginx
CMD ["nginx", "-g", "daemon off;"]
