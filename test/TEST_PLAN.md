# Test plan for the Topic Last Value Queue 


## Basic tests
You can use the utlilities in the python/ directory of this repo to complete these tests. They use aio_pika

### Aim:

Basic tests verifying that:

- slow consumers using a NORMAL QUEUE will see each value published for that topic
- fast consumers using a NORMAL QUEUE will see each value published for that topic
- slow consumers using a TLV QUEUE will skip 'intermediate' values for a topic (a routing key)
- fast consumers using a TLV QUEUE will see each value published for that topic

### Tests:

./producer.py topic 1 a  # 1 second delay on producing
./producer.py topic 2 b  # 2 second delay

./consumer.py queue1 topic 5 a b         # slow consumer, NORMAL queue, verify it prints all values (ie printed timestamps should slowly drift out of line)
./consumer.py queue2 topic 0.01 a b      # fast consumer, NORMAL queue, should see all values

./consumer_tlv.py queue3 topic 5 b     # slow consumer, TLV queue, verify it only prints new values (ie printed timestamps should roughly match)
./consumer_tlv.py queue4 topic 5 a     # same as above, different routing key
./consumer_tlv.py queue5 topic 0.04 a  # fast consumer, TLV queue, verify it prints ALL values


### Aim:

Test that consumers running faster than the producer receive all of the to up date information in virtually real time (ie no delay)

### Tests:

./producer.py topic 1 a b c     

./consumer_tlv.py queue1 topic 0.01 a b c
./consumer_tlv.py queue2 topic 0.01 a b c



### Aim:

Test that many values on one topic don't cause starvation to another topic, or vice versa. Ordering should be basically round robin delivery of topic values 

### Tests:

#### 1
./producer.py topic 0.01 a      #fast  
./producer.py topic 1 b      #medium
./producer.py topic 3 c      #slow

./consumer_tlv.py queue1 topic 5 a b c  # even slower!


#### 2

./producer.py topic 0.01 a b c d   # producer goes flat chat producing across lots of topics
./consumer_tlv.py queue1 topic 1 a b c d  # consumer fairly slow

Start and stop the producer -- letting the consumer drain the queue and then starting producer again


#### 3

 # producers go flat chat producing across lots of topics
./producer.py topic 0.01 a        
./producer.py topic 0.01 b
./producer.py topic 0.01 c
./producer.py topic 0.01 d

./consumer_tlv.py queue1 topic 1 a b c d  # consumer fairly slow

Start and stop individual producers, letting each topic drain from the queue then start the producer again


### Confirm
Ordering of values delivered / printed at the consumer should be basically round robin delivery of topic values
Values should always be 'latest' values -- ie the now and received timestamps should be super close



### Aim:

Test advanced topic routing features work ok


### Tests

./producer.py topic 0.01 a.1.x a.2.x a.3.y a.3.z 
./producer.py topic 1 b.1.x b.3.z b.5.z

./consumer_tlv.py queue1 topic 1 *.1.* 
./consumer_tlv.py queue2 topic 5 a.# 
./consumer_tlv.py queue3 topic 5 \#.z



### Aim:

Ensure TLV is working with prefetch count higher than 1


### Tests

To test different timing conditions and message flows for acks etc there's one test using a normal pika consumer, one using aio_pika

#### Normal pika

./producer.py topic 0.01 a b c d e f g h i j k l m n o p q r

./prefetch_pika_consumer.py

#### Confirm

Consumer uses prefetch of 10 and processes messages one per second. So after the first 10 print similar timestamps you should expect from there it will print timestamps that stay about 10 seconds in the past
Should observe a vaguely round robin / random looking delivery of topic keys


### Aim:

Ensure compatibility with direct, fanout and topic exchanges. 

### Tests

Topic exchanges were covered by all the above tests, not extra needed
Retest all the above (except advanced topic routing) using direct exchange
Retest all the above (except advanced topic routing) using fanout exchange. Tests will be basically valid but the 'binding' to routing keys will be ignored and all consumers will receive all types a, b, c etc



## 'Infrastructure' tests

After installing the plugin, start and stop the entire rabbitmq server (we had issues with it crashing on startup at one stage)

Check the plugin can be enabled and disabled, enabled again

Retest the above tests on single and multi-node rabbit clusters (1&3 nodes is sufficient)

Simulate a server crash, eg. by running one of the above 'basic' tests and force restarting the computer, check that rabbitmq boots up ok after (we previously had some issues with this, recovering from something like the Write Ahead Log (WAL))


