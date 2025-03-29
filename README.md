# price-comparison-backend
Deploy to AWS or DigitalOcean
--------------------------------------------------------------
Option 1: Deploy to AWS (ECS Fargate + RDS + ElastiCache)
Create an AWS RDS (PostgreSQL) Instance

Set up AWS ElastiCache for Redis

Deploy Kafka using AWS MSK (Managed Kafka)

Containerize the App with Docker

Deploy to AWS ECS Fargate

Dockerfile for Spring Boot App
📄 Dockerfile

dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/price-comparison-backend.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
ECS Deployment Steps
Push Docker Image to AWS ECR

sh
aws ecr create-repository --repository-name price-comparison-backend
aws ecr get-login-password | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com
docker tag price-comparison-backend:latest <AWS_ECR_REPO_URL>:latest
docker push <AWS_ECR_REPO_URL>:latest
Create AWS Fargate Task Definition

Run Task on ECS with RDS, ElastiCache, and MSK

---------------------------------------------------------------------------------
Option 2: Deploy to DigitalOcean (App Platform + Managed PostgreSQL + Redis)
Create DigitalOcean Droplet for Kafka

Use DigitalOcean Managed PostgreSQL & Redis

Deploy Spring Boot App using DigitalOcean App Platform

Deploy with Docker Compose
📄 docker-compose.yml

yaml
version: "3.8"
services:
  app:
    image: your-dockerhub-username/price-comparison-backend:latest
    ports:
      - "8080:8080"
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/price_db
      - SPRING_DATASOURCE_USERNAME=postgres
      - SPRING_DATASOURCE_PASSWORD=yourpassword
      - SPRING_KAFKA_BOOTSTRAP_SERVERS=kafka:9092
      - SPRING_REDIS_HOST=redis
    depends_on:
      - postgres
      - kafka
      - redis

  postgres:
    image: postgres:14
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=yourpassword
      - POSTGRES_DB=price_db

  kafka:
    image: wurstmeister/kafka
    environment:
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181

  redis:
    image: redis:6

  zookeeper:
    image: wurstmeister/zookeeper

Deploy using:

sh
docker-compose up -d
