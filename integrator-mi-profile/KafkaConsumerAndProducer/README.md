# Kafka Consumer and Producer

## Description

An event integration that publishes messages to a Kafka topic and consumes them again. `WeatherDataPublishAPI` publishes the request payload to the `weatherdatatopic` topic using the Kafka connector, and `WeatherDataTransmitInboundEP` consumes messages from the same topic and logs them through `WeatherDataProcessSeq`.

The integration connects to a Kafka cluster that is secured with SASL over TLS (`SASL_SSL` with the `PLAIN` mechanism), such as Confluent Cloud, so it runs the same way locally and on WSO2 Cloud.

## Prerequisites

1. A Kafka cluster that is reachable from where the integration runs and accepts `SASL_SSL` connections with the `PLAIN` mechanism.
2. A topic named `weatherdatatopic` in that cluster.
3. An API key and secret (or username and password) that can read from and write to the topic.

## Configuration

The integration reads the following values, declared in `src/main/wso2mi/resources/conf/config.properties`. Both are required.

- `kafkaBootstrapServers`: the bootstrap server address of the cluster, for example `pkc-xxxxx.us-east-1.aws.confluent.cloud:9092`.
- `kafkaSaslJaasConfig`: the full JAAS configuration that carries the credentials, in this form:

  ```
  org.apache.kafka.common.security.plain.PlainLoginModule required username="<API key>" password="<API secret>";
  ```

For a local run, set them as environment variables or create a `.env` file in the project directory:

```
kafkaBootstrapServers=pkc-xxxxx.us-east-1.aws.confluent.cloud:9092
kafkaSaslJaasConfig=org.apache.kafka.common.security.plain.PlainLoginModule required username="<API key>" password="<API secret>";
```

Do not commit the `.env` file.

## Usage Instructions

1. Run the integration locally using the Run button in WSO2 Integrator: MI.
2. Publish a message:

   ```bash
   curl -X POST http://localhost:8290/publishweatherdata \
     -H "Content-Type: application/json" \
     -d '{"city": "Colombo", "temperature": 30}'
   ```

   The API responds with the topic, partition, and offset the message was written to.
3. Check the server log. The inbound endpoint consumes the message and logs its payload.

### Deploy on **WSO2 Cloud**

1. Deploy this integration on **WSO2 Cloud** as an **Event Integration**.
2. Set `kafkaBootstrapServers` and `kafkaSaslJaasConfig` in the integration's configurations.

## Using an unsecured local broker

To test against a local Kafka broker without authentication, remove the `security.protocol`, `sasl.mechanism`, and `sasl.jaas.config` parameters from `WeatherDataTransmitInboundEP.xml` and the `securityProtocol`, `saslMechanism`, and `saslJaasConfig` elements from `KafkaConnection.xml`, and set `kafkaBootstrapServers` to `localhost:9092`.
