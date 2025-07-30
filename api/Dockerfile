# [Build stage] compile and package the application
FROM eclipse-temurin:21 AS builder

WORKDIR /app
COPY gradlew .
COPY gradle gradle
COPY build.gradle.kts settings.gradle.kts gradle.properties ./
COPY src src

RUN ./gradlew --no-daemon clean build

# [Development stage] enable hot-reload for local development
FROM eclipse-temurin:21 AS dev
WORKDIR /app

# ビルド済みの jar とソース、Gradle Wrapper を取り込む
COPY --from=builder /app/build/libs/*.jar ./app.jar
COPY --from=builder /app/gradlew ./gradlew
COPY --from=builder /app/gradle ./gradle

# バックグラウンドでソースファイルの変更をリアルタイムにビルド & アプリケーションを実行（developmentオプションでビルドファイルの変更を即時反映）
ENTRYPOINT ["bash", "-c", "./gradlew -t build & ./gradlew run -t -Dio.ktor.development=true"]

# [Runtime stage] lightweight production image
FROM eclipse-temurin:21-jre-jammy
WORKDIR /app

COPY --from=builder /app/build/libs/*.jar app.jar

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost:8080/health || exit 1

ENTRYPOINT ["java", "-jar", "app.jar"]