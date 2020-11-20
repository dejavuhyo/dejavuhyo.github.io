---
title: Mosquitto Publisher Subscriber
author: Hyosik
date: 2020-11-20 10:30:00 +0900
categories: [Tools, MQTT]
tags: [mosquitto_publisher, mosquitto_subscriber, mosquitto_pub, mosquitto_sub, matt_publisher, mqtt_subscriber, mqtt_pub, mqtt_sub, mqtt_broker, mqtt]
---

## 1. Eclipse Paho Client Mqttv3 Download

* Gradle

```gradle
compile group: 'org.eclipse.paho', name: 'org.eclipse.paho.client.mqttv3', version: '1.2.5'
```

* Maven

```xml
<dependency>
    <groupId>org.eclipse.paho</groupId>
    <artifactId>org.eclipse.paho.client.mqttv3</artifactId>
    <version>1.2.5</version>
</dependency>
```

## 2. Publisher

```java
import org.eclipse.paho.client.mqttv3.MqttClient;
import org.eclipse.paho.client.mqttv3.MqttConnectOptions;
import org.eclipse.paho.client.mqttv3.MqttException;
import org.eclipse.paho.client.mqttv3.MqttMessage;
import org.eclipse.paho.client.mqttv3.persist.MemoryPersistence;

import java.util.Scanner;

public class Publisher {

    public static void main(String[] args) {

        String brokerUrl = "tcp://localhost:1883";
        String clientId = "publisher";
        String id = "admin";
        String password = "admin123";
        String topic = "test";

        String content = null;

        int qos = 2;

        MemoryPersistence persistence = new MemoryPersistence();

        if (args.length > 1)
            brokerUrl = "tcp://" + args[0] + ":1883";

        try {
            MqttClient sampleClient = new MqttClient(brokerUrl, clientId, persistence);

            MqttConnectOptions conOpt = new MqttConnectOptions();
            conOpt.setCleanSession(true);
            conOpt.setUserName(id);
            conOpt.setPassword(password.toCharArray());

            System.out.println("Connecting to broker: " + brokerUrl);

            sampleClient.connect(conOpt);

            System.out.println("Connected");

            System.out.print("Input a Message : ");
            Scanner sc = new Scanner(System.in);
            content = sc.nextLine();

            System.out.println("Publishing message: " + content);

            MqttMessage message = new MqttMessage(content.getBytes());
            message.setQos(qos);
            sampleClient.publish(topic, message);

            System.out.println("Message published");

            sampleClient.disconnect();

            System.out.println("Disconnected");
            System.exit(0);

        } catch (MqttException me) {
            System.out.println("reason " + me.getReasonCode());
            System.out.println("msg " + me.getMessage());
            System.out.println("loc " + me.getLocalizedMessage());
            System.out.println("cause " + me.getCause());
            System.out.println("excep " + me);
            me.printStackTrace();
        }
    }
}
```

## 3. Subscriber

```java
import org.eclipse.paho.client.mqttv3.*;
import org.eclipse.paho.client.mqttv3.persist.MqttDefaultFilePersistence;

import java.sql.Timestamp;
import java.util.logging.Logger;

public class Subscriber {

    private static Logger logger = Logger.getGlobal();

    public static void main(String[] args) throws Exception {

        String brokerUrl = "tcp://192.168.0.214:1883";
        String clientId = "subcriber";
        String id = "admin";
        String password = "admin123";
        String topicName = "test";

        String tmpDir = System.getProperty("java.io.tmpdir");
        MqttDefaultFilePersistence dataStore = new MqttDefaultFilePersistence(tmpDir);

        String uri = System.getenv("CLOUDMQTT_URL");
        // Construct the connection options object that contains connection
        // parameters
        // such as cleanSession and LWT
        MqttConnectOptions conOpt = new MqttConnectOptions();
        conOpt.setCleanSession(true);
        conOpt.setUserName(id);
        conOpt.setPassword(password.toCharArray());

        // Construct an MQTT blocking mode client
        MqttClient client = new MqttClient(brokerUrl, clientId, dataStore);

        // Set this wrapper as the callback handler

        client.setCallback(new MqttCallback() {

            /**
             * @see MqttCallback#messageArrived(String, MqttMessage)
             */
            public void messageArrived(String topic, MqttMessage message) throws MqttException {
                // Called when a message arrives from the server that matches any
                // subscription made by the client
                String time = new Timestamp(System.currentTimeMillis()).toString();
                logger.info("");

                System.out.println("Time:\t" + time + "  Topic:\t" + topic + "  Message:\t" + new String(message.getPayload()) + "  QoS:\t" + message.getQos());
            }

            /**
             * @see MqttCallback#deliveryComplete(IMqttDeliveryToken)
             */
            public void deliveryComplete(IMqttDeliveryToken token) {
                // Called when a message has been delivered to the
                // server. The token passed in here is the same one
                // that was passed to or returned from the original call to publish.
                // This allows applications to perform asynchronous
                // delivery without blocking until delivery completes.
                //
                // This sample demonstrates asynchronous deliver and
                // uses the token.waitForCompletion() call in the main thread which
                // blocks until the delivery has completed.
                // Additionally the deliveryComplete method will be called if
                // the callback is set on the client
                //
                // If the connection to the server breaks before delivery has completed
                // delivery of a message will complete after the client has
                // re-connected.
                // The getPendingTokens method will provide tokens for any messages
                // that are still to be delivered.
            }

            /**
             * @see MqttCallback#connectionLost(Throwable)
             */
            public void connectionLost(Throwable cause) {
                // Called when the connection to the server has been lost.
                // An application may choose to implement reconnection
                // logic at this point. This sample simply exits.
                System.out.println("Connection to " + "localhost" + " lost!" + cause);
                System.exit(1);
            }
        });

        // Connect to the MQTT server
        client.connect(conOpt);

        // Create and configure a message
        MqttMessage message = new MqttMessage("Thankyou".getBytes());

        int qos = 2;
        message.setQos(qos);

        client.publish(topicName, message); // Blocking publish

        // Subscribe to the requested topic
        // The QoS specified is the maximum level that messages will be sent to
        // the client at.
        // For instance if QoS 1 is specified, any messages originally published
        // at QoS 2 will
        // be downgraded to 1 when delivering to the client but messages
        // published at 1 and 0
        // will be received at the same level they were published at.
        log("Subscribing to topic \"" + topicName + "\" qos " + qos);
        client.subscribe(topicName, qos);

    }

    /****************************************************************/
    /* Methods to implement the MqttCallback interface */

    /****************************************************************/

    public static void log(String log) {
        System.out.println("[Debug]" + log);
    }

    /****************************************************************/
    /* End of MqttCallback methods */

    /****************************************************************/

    static void printHelp() {
        System.out.println("Syntax:\n\n" + "    Sample [-h] [-a publish|subscribe] [-t <topic>] [-m <message text>]\n"
                + "            [-s 0|1|2] -b <hostname|IP address>] [-p <brokerport>] [-i <clientID>]\n\n"
                + "    -h  Print this help text and quit\n" + "    -q  Quiet mode (default is false)\n"
                + "    -a  Perform the relevant action (default is publish)\n"
                + "    -t  Publish/subscribe to <topic> instead of the default\n"
                + "            (publish: \"Sample/Java/v3\", subscribe: \"Sample/#\")\n"
                + "    -m  Use <message text> instead of the default\n"
                + "            (\"Message from MQTTv3 Java client\")\n"
                + "    -s  Use this QoS instead of the default (2)\n"
                + "    -b  Use this name/IP address instead of the default (localhost)\n"
                + "    -p  Use this port instead of the default (1883)\n\n"
                + "    -i  Use this client ID instead of SampleJavaV3_<action>\n"
                + "    -c  Connect to the server with a clean session (default is false)\n"
                + "     \n\n Security Options \n" + "     -u Username \n" + "     -z Password \n"
                + "     \n\n SSL Options \n" + "    -v  SSL enabled; true - (default is false) "
                + "    -k  Use this JKS format key store to verify the client\n"
                + "    -w  Passpharse to verify certificates in the keys store\n"
                + "    -r  Use this JKS format keystore to verify the server\n"
                + " If javax.net.ssl properties have been set only the -v flag needs to be set\n"
                + "Delimit strings containing spaces with \"\"\n\n"
                + "Publishers transmit a single message then disconnect from the server.\n"
                + "Subscribers remain connected to the server and receive appropriate\n"
                + "messages until <enter> is pressed.\n\n");
    }
}
```

## 출처 및 참고
* <https://iot.knu.ac.kr/tech/CPL-TR-16-02-MQTT.pdf>
