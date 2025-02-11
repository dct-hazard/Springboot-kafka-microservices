# Use multi-stage build to optimize image size
#FROM maven:3.8.4-openjdk-21 AS builder
#WORKDIR /app
#COPY . .
#RUN mvn clean package -DskipTests
#FROM openjdk:21-jdk-slim AS builder
#WORKDIR /app
#RUN apt-get update && apt-get install -y maven
#COPY . .
#RUN mvn clean install -pl base-domains -am -DskipTests
#RUN mvn clean package -DskipTests
#
## Use lightweight JDK image for final container
#FROM openjdk:21-jdk-slim
#WORKDIR /app
## Copy the correct JAR file from the builder stage
#COPY --from=builder /app/target/stock-service-0.0.1-SNAPSHOT.jar stock-service.jar
#EXPOSE 8081
#CMD ["java", "-jar", "stock-service.jar"]

FROM openjdk:21-jdk-slim AS builder
WORKDIR /app
RUN apt-get update && apt-get install -y maven

# Copy only base-domains first and install it
COPY base-domains /app/base-domains
WORKDIR /app/base-domains
RUN mvn clean install -DskipTests

# Copy and build stock-service
WORKDIR /app
COPY stock-service /app/stock-service
WORKDIR /app/stock-service
RUN mvn clean package -DskipTests

# Use lightweight JDK image for final container
FROM openjdk:21-jdk-slim
WORKDIR /app
COPY --from=builder /app/stock-service/target/stock-service-0.0.1-SNAPSHOT.jar stock-service.jar
EXPOSE 8081
CMD ["java", "-jar", "stock-service.jar"]

