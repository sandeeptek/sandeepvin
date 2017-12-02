## Requirement clarification:
Based on the discussion, since this is only turbine setup we need only 10 GB of EBS volume in the setup
-No Exchange / Queue are required
-Only a user creation is for application is sufficient
- Latest / Stage will have 1 Node instance
- Load / Prod will have 2 Node instance 

1. What is the amount of traffic / messages we are expecting on a busy day?

    We will be sending ~ 120 messages per minute considering number of services we have for phase 1, these numbers might increase/decrease based on auto scale of service instances, but I don't see anything going beyond 200 messages max per min in phase 1, and this will be constant mostly since our message from each service instances (Docker) will be 4 per min.
2. What is message payload size?

    These messages will be ~5KB per message.
  
3. Do we need any message back-up or resiliency features? 

   We don't need any resiliency feature for phase 1 since we can live with loss of these monitoring messages, in case we implement messaging for cache eviction we would need resiliency but we don't need it right now it can wait.
  
4. Do we need any message replay capability?

    We won't need it.
  
5. Do we need any message priority / order? 

   We don't need message order.
   
6. How many publishers’ instances will be connected to RabbitMQ?

    Publishers number could be max ~30 for phase 1 services.
    
7. How many consumer instances will be connected to RabbitMQ?

    For phase 1 it could be ~5.
    
8. How many Queues need to be created in RabbitMQ? 

    For now just 1 for Hystrix, for cache eviction might need another one, will let you know the exact names of queue when you are ready for implementation.
  
9. What is the timeframe expected to save the messages in Broker if consumers are down?  

    For Hystrix messages we don't need to save messages but for cache eviction message we would need but as of now I can't give exact details but we can assume it can be saved for few hours.
  
10. How we are connecting to RabbitMQ Infrastructure, Are we using RA API’S? 

    For hystrix events we will be using spring libraries since those are out of box support from spring cloud APIs but for cache eviction messages we will be using RA APIs when we implement
    
11. What is WBSE for RabbitMQ Design Work?

    You can use "1147528.CP.TM
