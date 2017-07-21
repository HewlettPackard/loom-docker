# loom-docker
Loom with pre-installed Docker adapter. For more information on Loom look at its [github repo](https://github.com/HewlettPackard/loom).

This project is configured to pull down the version of Loom specified in `pom.xml` and the required versions of the adapter(s) which will be placed in the `./adapters` folder.

Instructions for creating and running a containerised version are at the end.

## Building
Install Java 1.8

Install maven v3.0.5+ 

To build:

```
$ mvn clean install
```

## Configuring
This project includes the Docker adapter whose configuration can can be found in `./adapters/docker.properties` file.  To use a different version of the docker-adapter, modify the `docker-adapter.version` property in `pom.xml`.

You will also need to create a `hosts.json` file with details of each docker daemon you want to connect to.  See `example-docker-hosts.json` for an example.

### Using different versions
While not recommended, if you wish to experiment with other versions of Loom, Weaver or the adapters(s) then you must not only modify `pom.xml` but also `jetty.xml` to ensure that the correct versions are loaded following a successful maven build.

## Running
Tested with docker-ce 17.06.0-ce and spotify docker client 8.8.1.

> Note that the spotify docker client is used to talk to the docker daemons over TCP.  You will need to ensure that you have deployed your docker daemons to also **listen on TCP** (default port 2375), e.g. `-H fd:// -H tcp://0.0.0.0:2375`.

The provided `deployment.properties` file must be used.

```
$ java -Ddeployment.properties=deployment.properties -jar target\jetty-runner.jar --config jetty.xml target\loom-server.war
```

If you wish to modify the logging output you may override the `log4j.properties` file packaged with Loom by specifying your own when you launch Loom, e.g.

```
$ java -Dlog4j.configuration=file:<my log4j.properties file> -Ddeployment.properties=deployment.properties -jar target\jetty-runner.jar --config jetty.xml target\loom-server.war
```

An example log4j configuration is provided in `example-log4j.properties`.

Depending on the demands of the loaded adapters, i.e. the amount of data managed by Loom, you will need to increase the default JVM heap size and preallocate for best performance, e.g.

```
$ java -Xmx2048m -Xms2048m -Ddeployment.properties=deployment.properties -jar target\jetty-runner.jar --config jetty.xml target\loom-server.war
```

## Accessing Loom
The Weaver UI can be accessed via http://localhost:9099/weaver


## Docker

Three docker images can be built using this project.  To do this run:

```
$ ./docker-build.sh
```

The Dockerfiles and other config specific to the container builds are in `src/main/docker`.

### loom-docker

This image hosts all files in `/loom` and expects deployment-specific information to be in `/config`.  Specifically a JSON file containing the hosts configuration should be made available in `/config/hosts.json` and the credentials required to ssh to the hosts should be in `/config` using the name specified in `hosts.json`.  For example, assuming you have created a `hosts.json` in your working directory, you can start `loom-docker` so:

```
$ docker run -d -p 9099:9099 -v `pwd`:/config loom-docker
```
