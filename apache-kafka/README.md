Horizontal Scalibility
	- Can scale to 100s of brokers
	- Can scale to millions of messages per second
High performance (latency less thatn 10ms) - Real time

Use cases:
Messaging system
Activity tracking
Metrics 
Logs
streams processing
microservices pub/sub

cluster
	- topics 
		- its like table in database without contraint
		- can have as many
		- any kind of message format
		- the sequence of message called a data stream
		- can not query this, instead use producer to send data bad consumer will read that
		- are immutable, once data is written to partition it can not be changed
	- partitions and offsets
		- Topics are split in partitions
		- Messages within each partition are ordered
		- each message withing partition gets an incremental id, called offset
		- once data is written to a partition, it cannot be changed
		- data is kept only for limited time (default 1 week)
		- offset has meaning to certain partition only
		- order is guaranteed only withing partition
		- data is assigned randomly to a partition unless a key is provided
		- can have as many partitions per topic
		
		
Producers
	- writes data to topics
	- knows which partition to write to (and which kafka broker has it)
	- Message Keys
		- Producer can choose to send a key with the message, 
			if key is null data sent rount robin (partition 0,1,2, ..)
			else goes to same partition based on hashing (murmur2 algo > target parti = Math.Abs(Utils.murmur2(keyBytes)) % (numPartitions - 1) )
	- Kafka only accepts bytes as input
	- Can chose to receive acknowledgement of data writes
		- acks=0 > wont wait for acknowledgement (possible data loss)
		- acks=1 > will wait for leader acknowledgement (limited data loss)
		- acks=all > will wait for leader + all replica acknowledgement (no data loss)

Consumers
	- Read data from topic
	- Comsumers know which broker to read from
	- if broker fails, consumer know how to recover
	- data is read in order from low to high offset within each partition
	- ordering of messages only within partition
	- read bytes and deserialize it
	- Consumer groups >  Each consumer from group will read from exclusive partitions
	- Consumer offsets > 
		- Kafka stores the offsets at which a consumer group has been reading
		- The offset commited are in Kafka topic named __consumer_offsets
		- When consumer in group has processed data retreived from Kafka, it should be periodically committing the offset (broker will write to __consumer_offsets not group itself)
		  so if consumer dies it can able to read back from where it left off due to commited consumer offset
		  
Kafka Broker
	- Cluser is composed of mutliple brokers (Servers)
	- Each broker is identified with its ID 
	- Each broker contain certain topic partitions
	- After connecting to any broker (called bootstrap broker), you will be connected to the entire cluster
	- Cluster can have from 3 to 100 brokers
	- data is distributed across brokers (called as horizontal scalling)
	  e.g.  
	  - Broker 101 (Topic-A <Partition 0>, Topic-B <Partition 1>)
	  - Broker 102 (Topic-A <Partition 2>, Topic-B <Partition 0>)
	  - Broker 103 (Topic-A <Partition 1>)
    - broker discovery
		- Every broker known as bootstrap broker
		- That mean you only need to connect one broker and client will know how to connect to the entire cluster
		- Each broker knows about all brokers, topics and partitions (metadata)

Topic replication factor
	- Topic should have replication factor > 1 (2 to 3)
	- This way if a broker is down, another broker can still serve the data
	- e.g. Topic-A with 2 partitions and replication factor of 2
	  - Broker 101 (Topic-A <Partition 0>)
	  - Broker 102 (Topic-A <Partition 1>, Topic-A <Partition 0> {replication from Broker 101})
	  - Broker 103 (Topic-A <Partition 1> {replication from Broker 102})		
	
	
Leader for a Partition
	- At any time only one broker can be leader for a given partition
	- Producer can only send data to the broker that is leader of a partition, and other broker will replicate data
	  - Broker 101 (Topic-A <Partition 0>) //Leader for Partition 0
	  - Broker 102 (Topic-A <Partition 1>, Topic-A <Partition 0> {replication from Broker 101}) //Leader for Partition 
	  - Broker 103 (Topic-A <Partition 1> {replication from Broker 102})	
	  
Topic durability
	- replication factor of 3 can withstand 2 broker loss
	- so for replication factor of N, you can bare lose to N-1 brokers and recover your data
	
* If you want to change datatype of your message then create new topic instead to avoid deserialision problem

Zookeeper
	- Manages brokers (keeps list of them)
	- Helps in performing leader election in partition
	- Sends notifications to Kafka in case of changes (new topic, broker dies, delete topics)
	- Kafka 3.x can work without zookeeper (uses Raft instead) whereas Kafka 2.x can't work without zookeeper and Kafka 4.x will not have zookeeper
	- By design, it operated with odd number of server (1, 3, 5, 7)
	- Zookeeper has a leader (writes) and follower (reads)
	- Use it if you use brokers
	- Less secure than kafka
	- Never use Zookeeper as a configuration in your Kafka clients
	
KRaft
	- Zookeeper removed in 2020, due to scaling issues when cluster has > 100,000 partition
	- Kafka 3.x now implements the Raft protocol in order to replace Zookeeper