# Stage 1
FROM maven:3-openjdk-11-slim as build

WORKDIR /workspace/app

RUN apt-get update
RUN apt-get -y install git
RUN git clone https://github.com/opendatamesh-initiative/odm-platform.git

WORKDIR /workspace/app/odm-platform

RUN mvn clean install -DskipTests

# Stage 2
FROM openjdk:11-jre-slim

RUN apt-get update && apt-get install -y wget gpg lsb-release zip curl

ARG SPRING_PROFILES_ACTIVE=docker
ARG SPRING_PORT=8001
ARG JAVA_OPTS
ARG DATABASE_URL
ARG DATABASE_USERNAME
ARG DATABASE_PASSWORD
ARG FLYWAY_SCHEMA=ODMREGISTRY
ARG FLYWAY_SCRIPTS_DIR=postgresql
ARG H2_CONSOLE_ENABLED=false
ARG H2_CONSOLE_PATH=h2-console
ARG SKIP_METASERVICE=false
ARG METASERVICE_HOSTNAME
ARG METASERVICE_PORT
ARG SKIP_POLICYSERVICE=false
ARG POLICYSERVICE_HOSTNAME
ARG POLICYSERVICE_PORT

ENV SPRING_PROFILES_ACTIVE ${SPRING_PROFILES_ACTIVE}
ENV SPRING_PORT ${SPRING_PORT}
ENV JAVA_OPTS ${JAVA_OPTS}
ENV DATABASE_URL ${DATABASE_URL}
ENV DATABASE_USERNAME ${DATABASE_USERNAME}
ENV DATABASE_PASSWORD ${DATABASE_PASSWORD}
ENV FLYWAY_SCHEMA ${FLYWAY_SCHEMA}
ENV FLYWAY_SCRIPTS_DIR ${FLYWAY_SCRIPTS_DIR}
ENV H2_CONSOLE_ENABLED ${H2_CONSOLE_ENABLED}
ENV H2_CONSOLE_PATH ${H2_CONSOLE_PATH}
ENV SKIP_METASERVICE ${SKIP_METASERVICE}
ENV METASERVICE_HOSTNAME ${METASERVICE_HOSTNAME}
ENV METASERVICE_PORT ${METASERVICE_PORT}
ENV SKIP_POLICYSERVICE ${SKIP_POLICYSERVICE}
ENV POLICYSERVICE_HOSTNAME ${POLICYSERVICE_HOSTNAME}
ENV POLICYSERVICE_PORT ${POLICYSERVICE_PORT}

COPY --from=build /workspace/app/odm-platform/platform-core/dpds/target/odm-platform-core-dpds-*.jar /app/
COPY --from=build /workspace/app/odm-platform/product-plane-services/registry-api/target/odm-platform-pp-registry-api-*.jar /app/
COPY --from=build /workspace/app/odm-platform/utility-plane-services/notification-api/target/odm-platform-up-notification-api-*.jar /app/
COPY --from=build  /workspace/app/odm-platform/product-plane-services/registry-server/target/odm-platform-pp-registry-server-*.jar /app/

RUN ln -s -f /usr/share/zoneinfo/Europe/Rome /etc/localtime

CMD java $JAVA_OPTS -jar /app/odm-platform-pp-registry-server*.jar --spring.profiles.active=$SPRING_PROFILES_ACTIVE

EXPOSE $SPRING_PORT