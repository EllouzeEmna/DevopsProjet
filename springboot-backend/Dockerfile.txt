# Base image for Java
FROM openjdk:17-jdk-alpine
VOLUME /tmp

# Copy Spring Boot JAR file
ARG JAR_FILE=target/springboot-backend.jar
COPY ${JAR_FILE} app.jar

# Expose port for the application
EXPOSE 8080

# Run the application
ENTRYPOINT ["java", "-jar", "/app.jar"]
