# Build stage
FROM maven:3.9-eclipse-temurin-21 AS builder

WORKDIR /app

# Copy pom.xml and download dependencies
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copy source code and build
COPY src src
RUN mvn clean package -DskipTests -q

# Runtime stage
FROM eclipse-temurin:21-jre-jammy

WORKDIR /app

# Copy the JAR from builder stage
COPY --from=builder /app/target/backend-0.0.1-SNAPSHOT.jar app.jar

# Create non-root user for security
RUN useradd -m -u 1000 appuser && chown -R appuser:appuser /app
USER appuser

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD java -cp app.jar org.springframework.boot.loader.JarLauncher -version || exit 1

# Expose port (adjust if needed)
EXPOSE 8080

# Run the application
ENTRYPOINT ["java", "-jar", "app.jar"]