# Set the default shell
SHELL ["/bin/sh", "-c"]

# Stage 1: Build stage
FROM maven:3.8.5-openjdk-17-alpine AS build

# Set working directory
WORKDIR /app

# Copy pom.xml and source code
ADD pom.xml
ADD src ./src

# Build-time argument for JAR file name
ARG JAR_FILE = target/springboot-docker-practice-0.0.1-SNAPSHOT.jar

# Set environment variable for Maven build
ENV MAVEN_OPTS = "-Xmx512m"
ENV MAVEN_SKIP_TESTS = true

# Package the application
RUN mvn clean package -DskipTests=${MAVEN_SKIP_TESTS}

# Stage 2: Runtime Stage
FROM openjdk:17-jdk-alpine

# Create a non-root user
RUN adduser -D myUser

# Set working directory and change ownership to the new user
WORKDIR /app
RUN chown -R myUser /app

# Switch to the non-root user
USER myUser

# Build-time argument for JAR file name
ARG JAR_FILE = target/springboot-docker-practice-0.0.1-SNAPSHOT.jar

# Copy the packaged jar file from the build stage
COPY --from=build /app/${JAR_FILE} app.jar

# Expose port 8080
EXPOSE 8080

# Set environment variable for the application
ENV JAVA_OPTS = "-Xms256m -Xmx512m"

# Health check command to ensure the application is running
HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
    CMD curl --fail http://localhost:8080/actuator/health || exit 1

# Set labels to provide metadata about the image
LABEL maintainer = "Sarmistha Sarkar"
LABEL description = "Learn Dockerfile with Spring Boot Project"
LABEL version = "1.0"

# Run the application
ENTRYPOINT ["sh", "-c", "java ${JAVA_OPTS} -jar app.jar"]



# docker build --build-arg JAR_FILE = target/springboot-docker-practice-0.0.1-SNAPSHOT.jar -t springboot-docker-practice:latest