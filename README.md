# bank-deposit-ui

```
spring run app.groovy
```

Aside from standard Spring boot properties (eg: `--server.port=8081`), the following custom properties are supported:

* `api.host` - configures the domain to send the API request
* `api.proto` - configures the protocol to send the API request
* `api.port` - configures the port to send the API request

Per [Spring boot convention](http://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html), the custom properties can be set a number of ways.

Here's a full example:
```
mvn clean package && java -jar target/bank-deposit-ui-1.0-SNAPSHOT.jar --api.host="localhost" --api.proto="http" --api.port="8080" --server.port=8081
```

edit

Note you'll need to install the Spring Boot CLI https://spring.io/guides/gs/spring-boot-cli-and-js/#scratch

###Management Endpoints
GET /info - customized to show the scm and build related info
POST /shutdown - cause the container to stop. Useful for
shutting down at the end of build testing

##Build Properties
The following can be specified at build time to inject version information:

${BUILD_NUMBER}
${BUILD_URL}
${GIT_COMMIT}
