# Use multi-stage build to optimize image size
#FROM maven:3.8.4-openjdk-21 AS builder
#WORKDIR /app
#COPY . .
#RUN mvn clean package -DskipTests

FROM openjdk:21-jdk-slim AS builder
WORKDIR /app
RUN apt-get update && apt-get install -y maven

# Copy only base-domains first and install it
COPY base-domains /app/base-domains
WORKDIR /app/base-domains
RUN mvn clean install -DskipTests

# Copy and build order-service
WORKDIR /app
COPY order-service /app/order-service
WORKDIR /app/order-service
RUN mvn clean package -DskipTests

# Use lightweight JDK image for final container
FROM openjdk:21-jdk-slim
WORKDIR /app
COPY --from=builder /app/order-service/target/order-service-0.0.1-SNAPSHOT.jar order-service.jar
EXPOSE 8080
CMD ["java", "-jar", "order-service.jar"]

