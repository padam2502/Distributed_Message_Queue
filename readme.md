# Portal MQ : Distributed Message Queue from Scratch
This project depicts how a message queue functions in the real world. The implementation supports cluster mode of operation and aims for high availability with minimum consistency errors.

## Components:
1. Brokers: The brokers are HTTP servers built using flask, which communicate with other brokers using a control plane. They use sqlite as a database to store messages.
2. Control Plane : The control plane is a virtual layer in zookeeper where we store metadata and various stateful information to ensure proper coordination between different brokers.
3. Client API: The client API is a REST based Abstraction layer , that allows the user to import the message queue functionality via classes and objects represented by Producers and Consumers.
5. Distributed locks: Used in both the broker and client API to properly coordinate certain actions.

## Setup

### Zookeeper configuration
```
localhost:2181
```

### Broker Setup
```bash
python3 server.py 5000
python3 server.py 5001
python3 server.py 5002
```
### Tech Stack:
- Zookeeper (kazoo - python) 
- REST API (flask - python)
- Threading (python)
- SQLite (database)

### Why Zookeeper is important ?

Creates a reliable communication mechanism among different brokers to coordinate certain actions.

## Demo
Simulation of Sensors and Controllers in an IoT environment where Sensors are producers and Controllers are consumers.

### Sensors
```bash
python3 sensor.py temp 1 5
python3 sensor.py temp 1 2
python3 sensor.py temp 2 3
```

### Controllers
```bash
python3 controller.py temp_1
python3 controller.py temp_1
python3 controller.py temp_2
```

### Atleast Once Delivery
- `Producer -> Broker:` Producer keeps on retrying to send the message incase of network failure until it recieves an acknowledgement from broker.
- `Broker -> Consumer:` Broker first sends the message with its sequence number, only when consumer recieves the message it tells the broker to consume and only then the message is consumed and offset is incremented.

### Consumers Subscribe
- Consumers automatically sleep when there is no message in the queue.
- Whenever a new message arrives all the consumers are notified which are in sleep mode and they compete to consume a message from the queue.

### Broker Replication
- Shutdown leader and check which new leader gets elected and check if all operations have been executed.
- If running producers and consumers automatically aligns themselves to the new leader.
