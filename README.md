🚀 How to Write Dockerfiles for Different Tech Stacks
First remember:

🧠 Dockerfile structure (same for every stack)
    FROM <base-image>
    WORKDIR /app
    COPY . .
    RUN <install dependencies>
    EXPOSE <port>
    CMD ["run-command"]
  Only the base image + install steps + start command change per stack.

1. Dockerfile for Node.js / Express App
      FROM node:18-alpine
      WORKDIR /app
      COPY package*.json ./
      RUN npm install
      COPY . . 
      EXPOSE 3000     
      CMD ["npm", "start"]

   Why this works
    Alpine → small image
    Copy package.json first → caching
    install dependencies → faster rebuilds


🔵 2. Dockerfile for React / Next.js Frontend
  🟣 Option A — Development build (simple)
      FROM node:18-alpine
      WORKDIR /app
      COPY package*.json ./
      RUN npm install
      COPY . .
      EXPOSE 3000
      CMD ["npm", "start"]

  🟢 Option B — Production build (BEST practice ⭐)
     # build stage
      FROM node:18-alpine AS build
      WORKDIR /app
      COPY package*.json ./
      RUN npm install
      COPY . .
      RUN npm run build
    
    # serve stage
      FROM nginx:alpine
      COPY --from=build /app/build /usr/share/nginx/html
      EXPOSE 80
      CMD ["nginx", "-g", "daemon off;"]
    👉 This is called multi-stage build (very important for interviews)

🟠 3. Dockerfile for Python Flask App
      FROM python:3.10-slim
      WORKDIR /app
      COPY requirements.txt .
      RUN pip install -r requirements.txt
      COPY . .
      EXPOSE 5000
      CMD ["python", "app.py"]

🔴 4. Dockerfile for Java Spring Boot App
      FROM openjdk:17-jdk-slim
      WORKDIR /app
      COPY target/myapp.jar app.jar
      EXPOSE 8080
      CMD ["java", "-jar", "app.jar"]
    👉 Build jar first:
        mvn clean package

 🟡 5. Dockerfile for Static Website (HTML/CSS/JS)
      FROM nginx:alpine
      COPY . /usr/share/nginx/html
      EXPOSE 80
      CMD ["nginx", "-g", "daemon off;"]
      
              
