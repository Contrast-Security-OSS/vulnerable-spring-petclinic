FROM maven:3.9-eclipse-temurin-17@sha256:036d1a6f2965e4368157bb87f02cd31652a96918a26f7eb5ae45a0aa33f2cb8e AS builder
ADD WebApplication/pom.xml /petclinic-src/pom.xml
ADD WebApplication/src /petclinic-src/src
ADD WebApplication/mvnw /petclinic-src/mvnw
ADD ./contrast_security.yaml /petclinic-src/contrast_security.yaml

WORKDIR /petclinic-src

RUN --mount=type=cache,target=/root/.m2 mvn clean package -DskipTests=true

FROM eclipse-temurin:17-jre@sha256:07c1f0f0c3f3c714cea7a9250a8418ec329baa2cea1f77dcda2a7332ff937633
RUN mkdir -p /etc/contrast/java/

COPY --from=builder /petclinic-src/target/WebApplication-3.1.0-SNAPSHOT.jar /petclinic.jar
COPY --from=builder /petclinic-src/contrast_security.yaml /etc/contrast/java/contrast_security.yaml

RUN curl -Ls "https://download.java.contrastsecurity.com/latest/" -o /contrast.jar

EXPOSE 8080
CMD ["java","-javaagent:/contrast.jar","-Dagent.java.observe_tracing_sampling_limit=1000","-Dagent.java.observe_metrics_export_interval_ms=30000","-Dagent.java.observe_tracing_sampling_interval_ms=1","-jar", "/petclinic.jar","--spring.profiles.active=mysql","--spring.datasource.petclinic.url=jdbc:mysql://db-1/petclinic","--spring.datasource.pii.url=jdbc:mysql://db-1/pii"]
