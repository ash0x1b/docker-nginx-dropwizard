# Docker nginx with Dropwizard application [![Build Status](https://travis-ci.org/stevenalexander/docker-nginx-dropwizard.svg?branch=master)](https://travis-ci.org/stevenalexander/docker-nginx-dropwizard)

Example using Docker to create an nginx image and dropwizard image, then link them together so nginx acts as a reverse proxy for Dropwizard. Can be extended to link together multiple Dropwizard applications. Uses [Docker Compose](http://docs.docker.com/compose/) to create and configure the images.

![terminal gif](https://raw.githubusercontent.com/stevenalexander/docker-nginx-dropwizard/master/terminal.gif "terminal gif")

Requires:
* [Docker](https://www.docker.com/)
* [Boot2Docker](http://boot2docker.io/)
* [Docker-compose](http://docs.docker.com/compose/)
* JDK (to compile java file locally)
* [Gradle](https://gradle.org/) (for build automation)

To run locally:

```
gradle run
# ./go
```

To run containers:
```
gradle buildJar
docker-compose up -d

# retrieve your docker host IP from boot2docker
boot2docker ip

# curl dropwizard/nginx containers using docker host IP
curl http://192.168.59.103:8080/hello
curl http://192.168.59.103:8090/hello
```

# Details

The `docker-compose.yml` file configures the two images, creating a dropwizard container and linking it to an nginx container. With the link in place, docker creates a hosts entry for the dropwizard container which can be used in the nginx config `volumes-nginx-conf.d/default.conf` when setting up the reverse proxy.

Based Travis build on [moul/travis-docker](https://github.com/moul/travis-docker).

Had problems with Travis build, mainly accessing the nginx container to test. I think this is caused by Travis security settings. Also sometimes the build just times out when starting up the containers, not sure why. This makes using Travis for doing integration testing of the images impractial, which is a shame. Might work for small isolated containers doing individual tasks, but linked containers seems too much.

Tried using a client image to test the nginx/dropwizard images, as you cannot curl directly from Travis CI. Ideally, once Travis has started the containers in demonised form I would run a test script which uses curl/selenium to test the various endpoints exposed from nginx and hit dropwizard. If this needs to be done via the client then the results of the test can be output to a write-enabled volume and parsed to determine build result, as docker-compose will always return exit code 0 if the containers run.
