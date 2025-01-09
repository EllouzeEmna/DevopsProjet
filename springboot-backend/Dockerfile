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
COPY --from=build /app/target/BankApi.jar app.jar

# Expose the port the app will run on
EXPOSE 8080

# Run the application with dockerize
ENTRYPOINT ["dockerize", "-wait", "tcp://db:3306", "-timeout", "300s", "java", "-jar", "app.jar"]
