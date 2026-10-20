# Pub/Sub

Messaging pattern with 3 main parts: 

1. Publisher: Services that produce the messages
2. Subscriber: Services that want to receive those messages. They register their interest in a topic.
3. Broker: Sits in between and delivers a copy of the every message on a topic (a named channel for a particular kind of event) to each subscriber.

## Life of a message

1. Publish and Store
- Publisher sends message to a topic on the broker
- Broker stores it and sends an ack back to publisher

2. Deliver to every subscription
- Broker makes the message available to every subscription on that topic

3. Process and ack
- Each subscriber receives the message, processes it and sends an ack.
- If the subscriber crashes before ack, broker retries again. Thus, subscribers must be idempotent.

## Types

### Pull v/s Push

- Push 
    - Broker sends message to every subscriber, usually via an HTTP POST endpoint that the subscriber exposes.
    - Simple and no polling needed
    - Broker controls pace so subscriber needs to manage bursts. Subscriber endpoint must stay reachable.
    - Better for serverless fns and simple integrations.
- Pull
    - Subscriber polls the broker for new messages.
    - Subscriber controls its own pace.
    - But need to run polling workers.
    - Better for high throughput backend processing

### Ephemeral v/s Durable

- Ephemeral
    - When the publisher sends the message, Redis forwards it to whoever is subscribed at that moment and then message is gone. No replay.
    - Eg: Redis Pub/Sub
- Durable
    - Messages wait until subscriber acks or until a retention period.
    - Eg: Google Cloud Pub/Sub
- Replayable log
    - Keep messages until a fixed time in an append only log, regardless of whether anyone has read it.
    - Each subscriber tracks its position in the log through an offset. They can then rewind its offset and replay all events (again need idempotency)
    - Eg: Kafka

## Message Ordering

- In general, pub/sub systems don't guarantee that messages will arrive in order. Reasons:
    - Messages spread across many partitions
    - Retries can change order

### Ordering by key

- Preserve order for messages with the same key. 
- Key can be like an order id. So all messages associated with an order id arrive in order. 
- Messages with different order ids continue processing in parallel.

### Cost of ordering
- Reduced parallelism.
- If the same key receives far more traffic, then that can become a bottleneck.

### Handling out of order events

- Use version numbers
- Eg: if a subscriber has already applied version 5 of an order, it can safely drop a late version 4.

## Scaling Subscribers

- Suppose a given topic receives 50K events per second and a single analytics consumer cannot keep up.
- Create a *consumer group/ share subscription / competing consumers* which is a set of workers that share on subscription's work. 
- The grp still gets every event but inside a group, each event is handled by only one worker.

![](./images/pub-sub-scaling-consumers.png)

- Number of partitions (workers inside the grp) determine parallelism. 
- No of partitions = no of events being processed in parallel.

- Consumer lag measure how far behind the consumer is from the latest message in a partition.
- If its too behind, that means workers are not processing events fast enough and we need more workers / partitions.

## Publishing Reliably: The Outbox pattern

- Dual Write Problem
    - What if the order is placed and saved but the service crashes before sending the `OrderPlaced` event.
    - Now the order has been placed but downstream services will never know about it. 
    - Problem is happening because we need to update two separate systems.

- Solution: The Transactional Outbox
    - Save the order and the event in the same DB, in a single transaction. Either both happen or none.
    - Event goes into a separate `outbox` table.
    - A background process then reads the outbox table for any new rows and publishes them to the msg broker. If that process itself crashes, then it can resume and publish any remaining events.

![](./images/pub-sub-txn-outbox.png)


## When to use?

- Notifications
- Orders and payments
- Activity tracking

### Trade-offs

- Eventual consistency: because subscribers process events async, diff parts of the system may not be updated at the same time.
- No immediate response
- Harder debugging
