# Kafka KRaft Cluster with SASL Authentication and Kafbat UI

This project provides a Docker Compose setup for a 3-node Apache Kafka cluster using KRaft (Kafka Raft) for metadata management. It includes SASL PLAIN authentication for secure communication and Kafbat UI for cluster management and monitoring.

## Features

- **KRaft Mode**: Uses Kafka's built-in Raft consensus for controller quorum, eliminating the need for ZooKeeper.
- **SASL Authentication**: Configured with PLAIN mechanism for secure inter-broker and external client communication.
- **Multi-Node Cluster**: 3 Kafka brokers for high availability and fault tolerance.
- **Kafbat UI**: Web-based interface for managing and monitoring Kafka topics, brokers, and messages.
- **Docker Compose**: Easy deployment and management of the entire stack.

## Prerequisites

- Docker and Docker Compose installed on your system.
- At least 4GB of available RAM (recommended for running 3 Kafka brokers).
- Ports 19092, 29092, 39092, and 8080 must be available on your host machine.

## Quick Start

1. **Clone or download this repository** to your local machine.

2. **Navigate to the project directory**:
   ```bash
   cd Kafka-Krat-Kafbat
   ```

3. **Start the Kafka cluster and UI**:
   ```bash
   docker-compose up -d
   ```

   This command will:
   - Pull the necessary Docker images.
   - Start 3 Kafka brokers in KRaft mode.
   - Launch the Kafbat UI.

4. **Wait for the services to initialize**. This may take a few minutes for the first run.

5. **Access Kafbat UI**:
   - Open your web browser and go to `http://localhost:8080`
   - Configure the connection to your Kafka cluster using the external listener ports.

## Configuration

### Kafka Brokers

Each Kafka broker is configured with:
- **Node ID**: 1, 2, or 3
- **Listeners**:
  - CONTROLLER: Internal Raft communication
  - INTERNAL: Inter-broker communication
  - EXTERNAL: Client connections with SASL authentication
- **Ports**:
  - kafka-1: 19092 (external)
  - kafka-2: 29092 (external)
  - kafka-3: 39092 (external)

### SASL Authentication

- **Mechanism**: PLAIN
- **Credentials**:
  - Username: `admin`, Password: `admin-secret`
  - Username: `app`, Password: `app-secret`
- Configuration file: `kafka_jaas.conf`

### Connecting to Kafka

For external clients, connect to any of the broker ports (19092, 29092, or 39092) with SASL PLAIN authentication.

Example connection string:
```
localhost:19092,localhost:29092,localhost:39092
```

Use the credentials defined in `kafka_jaas.conf` for authentication.

## Usage

### Managing Topics

Use Kafbat UI or Kafka CLI tools to create and manage topics:

```bash
# Connect to a broker container
docker exec -it kafka-kraft-1 /bin/bash

# Create a topic
kafka-topics.sh --create --topic my-topic --bootstrap-server localhost:9094 --replication-factor 3 --partitions 3
```

### Producing and Consuming Messages

```bash
# Produce messages
kafka-console-producer.sh --topic my-topic --bootstrap-server localhost:9094 --producer.config /etc/kafka/client.properties

# Consume messages
kafka-console-consumer.sh --topic my-topic --bootstrap-server localhost:9094 --consumer.config /etc/kafka/client.properties --from-beginning
```

Note: You'll need to create client configuration files with SASL settings for authentication.

## Stopping the Cluster

To stop all services:

```bash
docker-compose down
```

To also remove volumes (data):

```bash
docker-compose down -v
```

## Troubleshooting

- **Port conflicts**: Ensure the required ports are not in use by other applications.
- **Memory issues**: If you encounter out-of-memory errors, increase Docker's memory allocation.
- **Authentication failures**: Verify the credentials in `kafka_jaas.conf` match your client configuration.
- **Cluster not forming**: Check the logs with `docker-compose logs kafka-1` to diagnose KRaft initialization issues.

## Contributing

Contributions are welcome! Please feel free to submit issues, feature requests, or pull requests.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

