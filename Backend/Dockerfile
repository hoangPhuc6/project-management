# ========================================================
# Giai đoạn 1: Build file JAR bằng Maven & JDK 17
# ========================================================
FROM maven:3.9.6-eclipse-temurin-17-alpine AS builder
WORKDIR /app

# 1. Tận dụng Docker Cache: Tải thư viện Maven trước
COPY pom.xml .
RUN mvn dependency:go-offline -B

# 2. Copy mã nguồn và đóng gói (bỏ qua test để tăng tốc độ build)
COPY src ./src
RUN mvn clean package -DskipTests

# ========================================================
# Giai đoạn 2: Runtime Image siêu nhẹ bằng JRE 17 Alpine
# ========================================================
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Copy file .jar đã đóng gói từ giai đoạn 1 sang
COPY --from=builder /app/target/*.jar app.jar

# Tạo trước các thư mục lưu trữ file nộp bài và tài liệu
RUN mkdir -p uploads/submissions uploads/tasks uploads/requirements

# Cấu hình giới hạn RAM để tránh bị sập (OOM Crash) trên Render gói Free 512MB
ENV JAVA_OPTS="-XX:+UseContainerSupport -Xmx350m -Xms200m -XX:MaxMetaspaceSize=128m"

EXPOSE 8080

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
