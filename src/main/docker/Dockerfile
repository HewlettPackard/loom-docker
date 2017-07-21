FROM debian:latest

RUN apt-get update && apt-get install -y \
	openjdk-8-jre-headless \
 && rm -rf /var/lib/apt/lists/*

COPY target /loom/target

COPY adapters /loom/adapters

# Ensure we expect config to be present in /config
COPY src/main/docker/docker.properties /loom/adapters

COPY src/main/docker/log4j.properties /loom/
COPY example-docker-hosts.json /loom/
COPY jetty.xml /loom/
COPY deployment.properties /loom/
COPY run.sh /loom/

EXPOSE 9099

WORKDIR /loom

ENTRYPOINT ["./run.sh"]
