# Spring Cloud Config Server

Spring Cloud Config Server example providing configuration from [Spring Cloud Config Server Example Properties](https://github.com/EightAugusto/spring-cloud-config-server-example-properties).

---
## Requirements

* Repository with properties files for Spring Cloud Config Server for example [this](https://github.com/EightAugusto/spring-cloud-config-server-example-properties).
* Docker 24.0.6
* Make 3.81
* Java 21
* Maven 3.9.5
* Replace the required fields on the .env file with your configuration, for example:

```bash
cat <<EOF > .env
APPLICATION=spring-cloud-config-server-example
APPLICATION_PORT=8888
DOCKER_NETWORK=eightaugusto

SPRING_CLOUD_CONFIG_PROPERTIES_REPOSITORY=https://github.com/EightAugusto/spring-cloud-config-server-example-properties
SPRING_CLOUD_CONFIG_PROPERTIES_REPOSITORY_LABEL=develop
SPRING_CLOUD_CONFIG_PROPERTIES_ENCRYPT_KEY=ExampleKey
EOF
```

---
## Run

```bash
source .env; \
mvn clean install; \
java \
-Dserver.port=${APPLICATION_PORT} \
-Dencrypt.key=${SPRING_CLOUD_CONFIG_PROPERTIES_ENCRYPT_KEY} \
-Dspring.cloud.config.server.git.uri=${SPRING_CLOUD_CONFIG_PROPERTIES_REPOSITORY} \
-Dspring.cloud.config.server.git.default-label=${SPRING_CLOUD_CONFIG_PROPERTIES_REPOSITORY_LABEL} \
-Dspring.cloud.config.server.git.search-paths={application} \
-Dspring.cloud.config.server.git.force-pull=true \
-Dspring.cloud.config.server.git.clone-on-start=true \
-jar target/${APPLICATION}.jar
```
or
```bash
mvn clean install; \
make docker.start
```

---
## Encrypt

To encrypt a value you can use the following (Replacing ... with your value to be encrypted):

```bash
source .env; \
ENCRYPTED_VALUE=$(curl --location --request POST "localhost:${APPLICATION_PORT}/encrypt" \
--header 'Content-Type: text/plain' \
--data-raw EightAugusto); \
echo ${ENCRYPTED_VALUE}
```

To decrypt a value you can use the following (Replacing ... with your value to be encrypted):

```bash
source .env; \
DECRYPTED_VALUE=$(curl --location --request POST "localhost:${APPLICATION_PORT}/decrypt" \
--header 'Content-Type: text/plain' \
--data-raw ${ENCRYPTED_VALUE}); \
echo $DECRYPTED_VALUE
```