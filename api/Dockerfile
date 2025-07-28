# Build stage
FROM eclipse-temurin:21 AS builder
WORKDIR /app
COPY gradlew .
COPY gradle gradle
COPY build.gradle.kts settings.gradle.kts gradle.properties ./
COPY src src

# Build the application
RUN ./gradlew --no-daemon clean build

# Runtime stage
FROM eclipse-temurin:21-jre-jammy
WORKDIR /app

# Copy built application from builder
COPY --from=builder /app/build/libs/*.jar app.jar

# Expose port
EXPOSE 8080

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost:8080/health || exit 1

# Run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
