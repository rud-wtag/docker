# Use Alpine Linux as the base image
FROM alpine:latest

# Install Nginx and other dependencies
RUN apk update && \
    apk add --no-cache nginx && \
    mkdir -p /run/nginx

# Copy Nginx configuration files
COPY nginx.conf /etc/nginx/nginx.conf
COPY default.conf /etc/nginx/conf.d/default.conf

# Expose port 80
EXPOSE 80

# Start Nginx server
CMD ["nginx", "-g", "daemon off;"]
