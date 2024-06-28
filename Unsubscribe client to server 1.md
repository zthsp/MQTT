## Unsubscribe client to server scenario

Unsubcribe:The client sends UNSUBSCRIBE packets to the server to unsubscribe from the topic.

## Fuzzing Points.

Adding exception data to unsubscribe request messages, deleting data, and entering incorrectly formatted data--Set the Packet Identifier to be randomly selected from 0-65535.

## Description
unsubscribe message variable header

![image-20240627162237664](C:\Users\acer\AppData\Roaming\Typora\typora-user-images\image-20240627162237664.png)

location:unsubscribe message variable header

unsubscribe control packets must contain a non-zero 16-bit packet identifier . Each time a client sends a new message , it must assign a message identifier  that is not currently in use. If a client wants to resend this particular control message, it must use the same identifier when it subsequently retransmits that message. After the client processes the corresponding acknowledgment of the packet, the packet identifier is released for reuse.
Scenario steps：
Establish  connection：
    The client establishes a connection with the MQTT server and sends CONNECT packets.
    The server returns a CONNACK packet, indicating that the connection has been successfully established.

Send an UNSUBCRIBE message：
    The client sends an UNSUBSCRIBE packet requesting to unsubscribe to one or more topics.
    UNSUBSCRIBE message consists of  Packet Identifier（message identifier）and one or more subject filters.

Received the UNSUBACK message：
    After receiving the UNSUBSCRIBE packet, the server processes the subscription request and returns the UNSUBACK packet.

​	The UNSUBACK message contains the Packet Identifier corresponding to the UNSUBSCRIBE message and the return code for each subscription request (Return Code).

## Pass Fail Criteria

UnSubscribe client to server scenario 1
-   Unresponsive, crash, error.
-   The payload of a UNSUBSCRIBE message must contain at least one pair of subject filter and QoS level field combinations. A UNSUBSCRIBE message without a payload is a violation of the protocol and the server should close the connection promptly.
-   If a PUBCOMP message is sent to the server in an abnormal state (e.g., it has been disconnected), the server is expected to handle the situation correctly without causing inconsistent or erratic behavior.
-   The current version of the protocol does not use the high six bits of the Requested QoS byte. If any bit in the payload is non-zero, or if the QoS is not equal to 0,1 or 2, the server must consider the UNSUBSCRIBE message to be illegal and close the network connection.
-   If the server receives a UNSUBSCRIBE message containing multiple subject filters, it must handle that as if it had received a series of multiple UNSUBSCRIBE messages, except that it needs to merge their responses into a single UNSUBACK message.

## Result 

phenomenon:

normal

log:2024-06-25

picture:

![image-20240627175526671](C:\Users\acer\AppData\Roaming\Typora\typora-user-images\image-20240627175526671.png)

