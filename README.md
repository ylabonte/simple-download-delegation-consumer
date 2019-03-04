
# ddc - <small>a simple Download Delegation Consumer</small>

A [Spring](https://spring.io/) based web service consuming delegated
downloads ([see json schema here](https://gist.github.com/ylabonte/79d36b4f17635d7661bcac75677cd216#file-downloadrequests-schema-json))
which are http `POST`ed as `Content-Type: application/json; charset=utf-8`
to the root route `http://<service-address>:1040/`.
A simple `GET` request to the same route gives a brief status of all
known downloads (current and history).

For an appropriate Chrome browser extension which is capable of 
delegating downloads from your browser to your instance of the ddc see: 
https://github.com/ylabonte/chrome-simple-download-delegator

This implementation is rudimentary and offers absolutely no security 
features! I strongly discourage from productive use (accessible from
the internet)!

## Run using docker via dockerhub
You can run the app by simply using the prebuilt docker image from 
dockerhub:
```bash
$ docker run -it --rm --name ddc \
     -v ${HOME}/Downloads:/root/Downloads \
     -p 1040:1040 labonte/ddc:latest \
```

## Run using self built docker image

Simply clone the repo, build the image and run the container as usual.
```bash
$ git clone https://github.com/ylabonte/ddc.git ddc
$ cd ddc
$ docker build ddc:latest .
$ docker run -it --rm --name ddc \
     -v ${HOME}/Downloads:/root/Downloads \
     -v $(pwd)/application.properties:/root/application.properties \
     -p 1040:1040 ddc:latest
``` 

## Run in your local JVM

### Requirements
* Java 8
* Gradle 5

### Run
```bash
$ gradle bootRun
```
This will build, test and run the project for you.

### Build
This will (re)build the project (`bootRun` also does). Useful if you
only want the JAR file for execution.
```bash
$ gradle build
```

### Test
Only run tests. Useful during development.
```bash
$ gradle test
```

## How it works

### Getting status
```bash
$ curl 'localhost:1040'
```

### Requesting download(s)
```bash
$ curl 'localhost:1040' \
-H 'Content-Type: application/json; charset=utf-8' \
-d '{
  "url": "http://example.com/info.txt",
  "header": ["Cookie: auth-session-cookie=some-hash"],
  "destination": "very-important/info.txt"
}'
```
This will try to download the file from `http://example.com/info.txt`
using the supplied http cookie header and saves it to
`very-important/info.txt` (relative to the configured output path).