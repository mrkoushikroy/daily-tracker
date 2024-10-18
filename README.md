Step-by-Step Setup of Apache Kafka on Windows and Writing Python Code for Producer and Consumer

1. Install Java

Kafka requires Java to run. You need to install the Java Development Kit (JDK).

	•	Download the JDK and install it.
	•	Set the JAVA_HOME environment variable:
	1.	Go to System Properties → Environment Variables.
	2.	Under System Variables, click New and add:
	•	Variable Name: JAVA_HOME
	•	Variable Value: C:\Program Files\Java\jdk-version
	3.	Add %JAVA_HOME%\bin to the Path variable in System Variables.

2. Download and Install Apache Kafka

	•	Download Kafka from the official website.
	•	Extract the downloaded files into a directory like C:\kafka.

3. Configure and Start Kafka

Kafka requires both Zookeeper and Kafka servers to be running.

	1.	Start Zookeeper:
	•	Navigate to the Kafka directory: C:\kafka.
	•	Open config/zookeeper.properties and ensure the data directory path is correct, or create the folder if necessary.
	•	Open a new command prompt and run:

.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties


	2.	Start Kafka:
	•	Open a second command prompt in the same C:\kafka folder.
	•	Run:

.\bin\windows\kafka-server-start.bat .\config\server.properties



Kafka should now be running on your local machine.

4. Install Kafka Python Client

In your Python environment, install kafka-python:

pip install kafka-python

5. Python Code for Producer

The producer will send messages to Kafka.

from kafka import KafkaProducer
import json
import time

def json_serializer(data):
    return json.dumps(data).encode("utf-8")

producer = KafkaProducer(
    bootstrap_servers=['localhost:9092'],
    value_serializer=json_serializer
)

if __name__ == "__main__":
    for i in range(10):
        message = {"number": i}
        producer.send("test-topic", message)
        print(f"Sent: {message}")
        time.sleep(1)
    producer.flush()  # Ensure all messages are sent before exiting

This producer will send JSON-formatted messages to a Kafka topic named test-topic.

6. Python Code for Consumer

The consumer will read messages from the Kafka topic.

from kafka import KafkaConsumer
import json

consumer = KafkaConsumer(
    'test-topic',
    bootstrap_servers=['localhost:9092'],
    auto_offset_reset='earliest',
    enable_auto_commit=True,
    group_id='consumer-group-1',
    value_deserializer=lambda x: json.loads(x.decode('utf-8'))
)

if __name__ == "__main__":
    for message in consumer:
        print(f"Received: {message.value}")

7. Create a Kafka Topic

To create the test-topic used by the producer and consumer, use the following command:

	1.	Open a new terminal, go to C:\kafka, and run:

.\bin\windows\kafka-topics.bat --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1

.\bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test-topic

8. Testing the Setup

	1.	Start the producer code.
	2.	Start the consumer code.
	3.	The consumer will receive messages sent by the producer.

This completes the setup of Kafka on Windows and the Python code for message queueing with producers and consumers.