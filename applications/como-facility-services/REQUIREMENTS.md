##Requirement Clarification Details:

**1.What is the amount of traffic / messages we are expecting on a busy day?**

> The number/size/priority etc is the same as the existing production Facility DataLoader application. I know the messages are somewhat large and somewhat infrequent, if that helps.
 
**2.What is message payload size ?**


> Payload size can be up to several megabytes - this is the OneSource Facility message, used to populate/replace/update the Facility database. 

**3.Do we need any message back-up or resiliency features?**

> As long as the message is delivered OK we should not need backup or replay features. The Loader process inserts the entire message plus some metadata into a DB clob column.
 
**4.Do we need any message replay capability?**

> Replay functionality is handled from there(Point 3), including error fixing, based on processing status.

**5.Do we need any message priority / order ?**

> Messages should be processed in the order received, since there are incremental database changes. Each message has a timestamp, the oldest is processed first through a periodic process, but if an earlier message takes too long to arrive it may be processed out of order. All have the same priority.

**6.How many publishers instances will be connected to RabbitMQ?**

> 
- Only one publisher and consumer instance attached to each queue. It is actually synchronous request/acknowledge rather than pub/sub architecture.
> 
> 
- EAI publishes (routes) from OneSource to the RQ queue, listener routes from IBM RQ to RabbitMQ RQ queue. Loader listens on RQ queue, retrieves the message, send ack RS message to RabbitMQ. 
> 
> 
- Listener picks up RS response message on Rabbit, routes back to IBM RS queue. Typical composite MQ architecture.

**7.How many consumer instances will be connected to RabbitMQ?**

> Only one publisher and consumer instance attached to each queue

**8.How many Queues needs to be created in RabbitMQ ?**

> For RabbitMQ Queues - one request queue and one response queue

**9.What is the timeframe expected to save the messages in Broker if consumers are down?**


**10.How we are connecting to RabbitMQ Infrastructure, Are we using RA API’s**
  
> Using RA APIs to connect - Spring Integration with AMQP - a modified version of the Message Bridge application used by MM+.

**11.What is WBSE for RabbitMQ Design work?**