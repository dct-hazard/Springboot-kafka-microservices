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

# Copy and build email-service
WORKDIR /app
COPY email-service /app/email-service
WORKDIR /app/email-service
RUN mvn clean package -DskipTests

# Use lightweight JDK image for final container
FROM openjdk:21-jdk-slim
WORKDIR /app
COPY --from=builder /app/email-service/target/email-service-0.0.1-SNAPSHOT.jar email-service.jar
EXPOSE 8082
CMD ["java", "-jar", "email-service.jar"]

