# wdpr-ra-rmq-design
## Index
1. [Overview](#1-Overview)
2. [Steps](#2-Steps)
3. [Mapping Details](#3-MappingDetails)
4. [Snapshot Details](#3-SnapshotDetails)

## 1. Overview
Here we are providing the RabbitMQ configuration for migeration the application to latest environment. 

## 2. Steps

1. Download the existing broker configuration of RabbitMQ as JSON file from the current environment.

2. Import the existing configuration file into local RabbitMQ.

3. Create a new virtual host in RabbitMQ 

4. Create new Exchange, Queue as per the existing configuration and provide proper bindings.

5. Finally create a mapping file as show below.


## 3. Mapping Details

Segment | Exchange | Exchange Type | Queue | Routing Key
:-------|:--------|:-------|:------|:-------
LATEST|OMNICART.CARTEVENT|Direct| OMNICART.CARTEVENT.NIFI|omnicart
LATEST | WorkExchange.NAP7LATESTA | Direct | omnicart.NAP7LATESTA | omnicart
LATEST | WorkExchange.NAP7LATESTB | Direct | omnicart.NAP7LATESTB | omnicart
LATEST | RetryExchange.NAP7LATESTA | Fanout | omnicart.retry.NAP7LATESTA |
LATEST | RetryExchange.NAP7LATESTB| Fanout | omnicart.retry.NAP7LATESTB |



## 4. Snapshot Details

- Node details - We have 4 nodes configured as shown below.

<p align="center">
  <img src=Documents\Images\nodeDetails.JPG alt="functions"/>
</p>

- Ports and contexts - Here we have list of node and its binding port details.
<p align="center">
  <img src=Documents\Images\portDetails.JPG alt="functions"/>
</p>

- Exchanges - We have a list of exchange with the message type and the policy applied.
<p align="center">
  <img src=Documents\Images\exchangeDetails.JPG alt="functions"/>
</p>

- Queues - we have list of queue with the current virtual host mapping and the features applied on each queue.

<p align="center">
  <img src=Documents\Images\queueDetails.JPG alt="functions"/>
</p>

-  Users - Currently configured user details.
<p align="center">
  <img src=Documents\Images\userDetails.JPG alt="functions"/>
</p>

-  Virtual Hosts - Currently configured virtual host details
<p align="center">
  <img src=Documents\Images\hostDetails.JPG alt="functions"/>
</p>

-  Policies - currently configured policy details.
<p align="center">
  <img src=Documents\Images\policyDetails.JPG alt="functions"/>
</p>
