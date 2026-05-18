FROM maven:3.9.6-eclipse-temurin-17-alpine AS build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
RUN addgroup -S innovatech && adduser -S innovatech -G innovatech
USER innovatech:innovatech

COPY --from=build /app/target/*.jar app.jar

ENV JAVA_OPTS="-XX:+UseG1GC -XX:+ExitOnOutOfMemoryError"
EXPOSE 8081

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]