# Build Stage
FROM maven:3.8-openjdk-11 AS builder
WORKDIR /build
COPY pom.xml .
# 假设有 src 代码 (模拟)
COPY src ./src
# 跳过测试以加快构建
RUN mvn clean package -DskipTests

# Runtime Stage
FROM openjdk:11-jre-slim
WORKDIR /app
COPY --from=builder /build/target/*.jar app.jar

# 关键优化：让 JVM 感知容器内存限制 (CGroup)，防止 OOM
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0"

EXPOSE 8080
ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
