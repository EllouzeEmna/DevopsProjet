# Use a Maven image with OpenJDK 17 for the build stage
FROM maven:3.9.5-eclipse-temurin-17 AS build

# Set the working directory inside the container
WORKDIR /app

# Copy the pom.xml and source code into the container
COPY pom.xml .
COPY src ./src

# Build the application
RUN mvn clean install

# Use a slim OpenJDK image for the runtime stage
FROM eclipse-temurin:17-jdk-slim

# Set the working directory in the container
WORKDIR /app

# Copy the built JAR file from the build stage
COPY --from=build /app/target/springboot-backend.jar app.jar

# Install dockerize for waiting on dependencies (e.g., MySQL)
RUN apk add --no-cache wget && \
    wget https://github.com/jwilder/dockerize/releases/download/v0.6.1/dockerize-linux-amd64-v0.6.1.tar.gz && \
    tar -C /usr/local/bin -xzvf dockerize-linux-amd64-v0.6.1.tar.gz && \
    rm dockerize-linux-amd64-v0.6.1.tar.gz

# Expose the port the app will run on
EXPOSE 8080

# Run the application with dockerize
ENTRYPOINT ["dockerize", "-wait", "tcp://mysql:3306", "-timeout", "300s", "java", "-jar", "app.jar"]
