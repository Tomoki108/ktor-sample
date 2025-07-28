# [Build stage]
FROM eclipse-temurin:21 AS builder
WORKDIR /app
COPY gradlew .
COPY gradle gradle
COPY build.gradle.kts settings.gradle.kts gradle.properties ./
COPY src src

## Build the application
RUN ./gradlew --no-daemon clean build

# [Runtime stage]
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

# [Development stage]
FROM eclipse-temurin:21 AS dev
WORKDIR /app

# ビルド済みの jar とソース、Gradle Wrapper を取り込む
COPY --from=builder /app/build/libs/*.jar ./app.jar
COPY --from=builder /app/gradlew ./gradlew
COPY --from=builder /app/gradle ./gradle

# バックグラウンドでソースファイルの変更をリアルタイムにビルド & アプリケーションを実行（developmentオプションでビルドファイルの変更を即時反映）
ENTRYPOINT ["bash", "-c", "./gradlew -t build & ./gradlew run -t -Dio.ktor.development=true"]
