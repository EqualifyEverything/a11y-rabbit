# rabbitmq

🐰 Dockerized RabbitMQ for GovA11y 🐰

# A11yRabbit

🐰 Dockerized RabbitMQ for EqualifyApp 🐰

[![🏗️📤 Build and publish 🐳 images](https://github.com/EqualifyApp/a11y-rabbit/actions/workflows/containerize.yml/badge.svg)](https://github.com/EqualifyApp/a11y-rabbit/actions/workflows/containerize.yml)

A11yRabbit is a pre-configured Docker image that provides RabbitMQ server for the EqualifyApp.

## Usage

This Docker image provides a pre-configured RabbitMQ server for EqualifyApp. To use A11yRabbit, simply run the container to start the server. The RabbitMQ management console is available at http://localhost:15672 using the username and password (bhensel/big_boss).

```sh
docker run -d --name a11y-rabbit -p 5672:5672 -p 15672:15672 equalifyapp/a11y-rabbit
```

## Queues

RabbitMQ is being used to manage queues for various types of scans and crawls performed by the gova11y system. The queues are organized by their function, with launch* queues being used to hold URLs and data ready for checks, landing* queues storing the results of those checks, and oops queues holding error messages from the system.

All queues are durable and have a message time-to-live of 7200000 milliseconds (2 hours).

### `launch_` Queues

The launch\_ queues hold URLs and data ready for checks performed by the gova11y system. The queues are as follows:

| Queue Name               | Purpose                            |
| ------------------------ | ---------------------------------- |
| `launch_axe`             | Axe accessibility scanner          |
| `launch_tech`            | Wappalyzer tech check              |
| `launch_uppies`          | Site header and reachability check |
| `launch_crawler`         | Kraken site crawler                |
| `launch_crawler_sitemap` | Sitemap crawler                    |

All queues are durable, meaning that they will persist through server restarts, and have a message time-to-live of 7200000 milliseconds (2 hours).

### `landing_` Queues

The landing\_ queues hold the results of the checks performed by the gova11y system. The queues are as follows:

| Queue Name                | Purpose                                          |
| ------------------------- | ------------------------------------------------ |
| `landing_axe`             | Result of the axe accessibility scan             |
| `landing_tech`            | Result of the Wappalyzer tech check              |
| `landing_uppies`          | Result of the site header and reachability check |
| `landing_crawler`         | Result of the Kraken site crawler                |
| `landing_crawler_sitemap` | Result of the sitemap crawler                    |

All queues are durable, meaning that they will persist through server restarts, and have a message time-to-live of 7200000 milliseconds (2 hours).

### `oops` Queues

The oops queues hold error messages generated by the gova11y system. The queues are as follows:

| Queue Name     | Purpose                               |
| -------------- | ------------------------------------- |
| `oops_matrix`  | Errors from the Matrix                |
| `oops_workers` | Errors from the workers               |
| `oops_hole`    | Insert worker results into PostgreSQL |

## Ports & Volumes

RabbitMQ is a messaging broker that implements the Advanced Message Queuing Protocol (AMQP). It uses ports and volumes to communicate and store data.

### Ports

The following are the ports used by RabbitMQ:

| Port  | Purpose                                                                                                                                                                                                           |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 4369  | Used for the Erlang Distribution Protocol, which is used by RabbitMQ for clustering and node communication.                                                                                                       |
| 5672  | Default AMQP port used by RabbitMQ for client connections. Applications use this port to send and receive messages.                                                                                               |
| 15672 | Used for the RabbitMQ management interface. It provides a web-base interface to manage and monitor RabbitMQ.                                                                                                      |
| 15692 | Used for RabbitMQ's Prometheus exporter. It exposes metrics about RabbitMQ in a format that can be consumed by Prometheus or other monitoring systems. The exporter is enabled by the rabbitmq_prometheus plugin. |

### Volumes

**/var/lib/rabbitmq**: This volume contains RabbitMQ's data directory. It includes queues, exchanges, messages, and other data that RabbitMQ uses to store its state. You can mount this volume to persist RabbitMQ's data across container restarts.

## Accounts & Permissions

The system has a single vhost named gova11y. The permissions section specifies the access permissions for each user for this vhost. All users have full permissions (configure, write, and read) for all queues in the gova11y vhost.

| Username                 | Password                 | Purpose                                                     | Permissions                  |
| ------------------------ | ------------------------ | ----------------------------------------------------------- | ---------------------------- |
| `yeeter_in_chief`        | `yeet_for_love`          | Selects from database for `launch_` queues                  | `configure`, `write`, `read` |
| `worker_uppies`          | `pass_the_uppies_please` | `uppies` check worker nodes                                 | `configure`, `write`, `read` |
| `rabbit_hole`            | `lets_insert_things`     | Rabbit hole for inserting `landing_` queues into PostgreSQL | `configure`, `write`, `read` |
| `bhensel`                | `big_boss`               | Big boss admin                                              | `configure`, `write`, `read` |
| `worker_axe`             | `drop_the_axe`           | `Axe` a11y check nodes                                      | `configure`, `write`, `read` |
| `worker_tech`            | `whatchu_got`            | `Wappalyzer` tech nodes                                     | `configure`, `write`, `read` |
| `worker_crawler`         | `map_the_world`          | Site crawler nodes                                          | `configure`, `write`, `read` |
| `worker_crawler_sitemap` | `explore_the_unknown`    | Sitemap crawler nodes                                       | `configure`, `write`, `read` |

## Installation

To install and use this Docker image, follow these steps:

1. Clone the repository to your local machine.
2. Navigate to the cloned directory.
3. Build the Docker image using the following command:

```sh
docker build -t equalifyapp/a11y-rabbit .
```

4. Run the Docker container using the following command:

```bash
docker run -d --name a11y-rabbit -p 5672:5672 -p 15672:15672 equalifyapp/a11y-rabbit
```

5. Access the RabbitMQ management console at http://localhost:15672 using the default username and password (guest/guest).

## Repo Overview

### rabbitmq.conf

RabbitMQ configuration file.

### definitions.json

Pre-defined RabbitMQ users, vhosts, and permissions.

### Dockerfile

Dockerfile used to build the a11y-rabbit image.

Contributing

To contribute to this repository, please see our contributing guidelines in the 'CONTRIBUTING.md' file.
