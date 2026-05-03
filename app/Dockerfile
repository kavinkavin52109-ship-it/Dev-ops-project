# Use lightweight Nginx image
FROM nginx:alpine

# Copy project files into Nginx web root
COPY . /usr/share/nginx/html

# Expose container port
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]

