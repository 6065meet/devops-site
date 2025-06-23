# Use NGINX as the base image
FROM nginx:alpine

# Remove default NGINX static content
RUN rm -rf /usr/share/nginx/html/*

# Copy your static website content
COPY . /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start NGINX
CMD ["nginx", "-g", "daemon off;"]

