# Use official nginx alpine image
FROM nginx:alpine

# Copy the HTML file to nginx default directory
COPY index.html /usr/share/nginx/html/

# Expose port 80
EXPOSE 80

# nginx runs automatically, no CMD needed