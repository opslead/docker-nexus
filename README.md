# Sonatype Nexus Container Image

[![Docker Stars](https://img.shields.io/docker/stars/opslead/nexus.svg?style=flat-square)](https://hub.docker.com/r/opslead/nexus) 
[![Docker Pulls](https://img.shields.io/docker/pulls/opslead/nexus.svg?style=flat-square)](https://hub.docker.com/r/opslead/nexus)

#### Docker Images

- All images based on Fedora Linux
- [GitHub actions builds](https://github.com/opslead/docker-nexus/actions) 
- [Docker Hub](https://hub.docker.com/r/opslead/nexus)


#### Environment Variables
When you start the Nexus image, you can adjust the configuration of the instance by passing one or more environment variables either on the docker-compose file or on the docker run command line. The following environment values are provided to custom Nexus:

| Variable                  | Default Value | Description                     |
| ------------------------- | ------------- | ------------------------------- |
| `JAVA_ARGS`               |               | Configure JVM params            |

#### Persisting Nexus data

If you remove the container all your data and configurations will be lost, and the next time you run the image the database will be reinitialized. To avoid this loss of data, you should mount a volume that will persist even after the container is removed.

For persistence you should mount a volume at the `/opt/nexus/data` path. The above examples define a docker volume namely `nexus_data`. The Nexus application state will persist as long as this volume is not removed.

To avoid inadvertent removal of this volume you can mount host directories as data volumes. Alternatively you can make use of volume plugins to host the volume data.

#### Run the Service

```bash
docker service create --name nexus \
  -p 8081:8081 \
  -e JAVA_ARGS="-Xms2G -Xmx6G" \
  --mount type=bind,source=/data/nexus,destination=/opt/nexus/data \
  opslead/nexus:latest
```

When running Docker Engine in swarm mode, you can use `docker stack deploy` to deploy a complete application stack to the swarm. The deploy command accepts a stack description in the form of a Compose file.

```bash
docker stack deploy -c nexus-stack.yml nexus
```

Compose file example:
```
version: "3.8"
services:
  nexus:
    image: opslead/nexus:latest
    ports:
      - 8081:8081
     volumes:
      - nexus_data:/opt/nexus/data
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure
      environment:
        - JAVA_ARGS=-Xms2G -Xmx6G

volumes:
  nexus_data:
    driver: local
    driver_opts:
      type: "none"
      o: "bind"
      device: "/data/nexus"
```


# Contributing
We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/opslead/docker-nexus/issues), or submit a [pull request](https://github.com/opslead/docker-nexus/pulls) with your contribution.

# Issues
If you encountered a problem running this container, you can file an [issue](https://github.com/opslead/docker-nexus/issues). For us to provide better support, be sure to include the following information in your issue:

- Host OS and version
- Docker version
- Output of docker info
- Version of this container
- The command you used to run the container, and any relevant output you saw (masking any sensitive information)
