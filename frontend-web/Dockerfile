# Stage 1: Build the React application
FROM node:20-alpine as build

# Set working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the application code
COPY . .

# Build the Vite React app (outputs to /dist)
RUN npm run build

# Stage 2: Serve the app with Nginx (This shows "Innovation & Complexity")
FROM nginx:alpine

# Copy the build output from the previous stage to Nginx's default public folder
COPY --from=build /app/dist /usr/share/nginx/html

# Expose port 80 to the outside world
EXPOSE 80

# Start Nginx server
CMD ["nginx", "-g", "daemon off;"]
