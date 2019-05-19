Ref:
[[https://docs.mongodb.com/manual/sharding/]{.underline}](https://docs.mongodb.com/manual/sharding/)

[[http://learnmongodbthehardway.com/schema/sharding/]{.underline}](http://learnmongodbthehardway.com/schema/sharding/)

**Replication** is the process of synchronizing data across multiple
servers. Replication provides redundancy and increases data availability
with multiple copies of data on different database servers. Replication
protects a database from the loss of a single server. Replication also
allows you to recover from hardware failure and service interruptions.
With additional copies of the data, you can dedicate one to disaster
recovery, reporting, or backup.

Why Replication?

• To keep your data safe

• High (24\*7) availability of data

• Disaster recovery

• No downtime for maintenance (like backups, index rebuilds, compaction)

• Read scaling (extra copies to read from)

• Replica set is transparent to the application

A typical diagram of MongoDB replication is shown in which client
application always interact with the primary node and the primary node
then replicates the data to the secondary nodes.

mongod \--port \"PORT\" \--dbpath \"YOUR\_DB\_DATA\_PATH\" \--replSet
\"REPLICA\_SET\_INSTANCE\_NAME\"

Example

mongod \--port 27017 \--dbpath \"D:\\set up\\mongodb\\data\" \--replSet
rs0 \# starts a mongod instance with the name rs0, on port 27017.

\>rs.add("mongod1.net:27017")

Sharding is the process of storing data records across multiple machines
and it is MongoDB\'s approach to meeting the demands of data growth. As
the size of the data increases, a single machine may not be sufficient
to store the data nor provide an acceptable read and write throughput.
Sharding solves the problem with horizontal scaling. With sharding, you
add more machines to support data growth and the demands of read and
write operations.

Sharding is a concept in MongoDB, which splits large data sets into
small data sets across multiple MongoDB instances. The collection which
could be large in size is actually split across multiple collections or
Shards as they are called. Logically all the shards work as one
collection.

\*Throughput: the amount of material or items passing through a system
or process.

There are two methods for addressing system growth: vertical and
horizontal scaling.

***Vertical Scaling*** involves increasing the capacity of a single
server, such as using a more powerful CPU, adding more RAM, or
increasing the amount of storage space. Limitations in available
technology may restrict a single machine from being sufficiently
powerful for a given workload. Additionally, Cloud-based providers have
hard ceilings based on available hardware configurations. As a result,
there is a practical maximum for vertical scaling.

***Horizontal Scaling*** involves dividing the system dataset and load
over multiple servers, adding additional servers to increase capacity as
required. While the overall speed or capacity of a single machine may
not be high, each machine handles a subset of the overall workload,
potentially providing better efficiency than a single high-speed
high-capacity server. Expanding the capacity of the deployment only
requires adding additional servers as needed, which can be a lower
overall cost than high-end hardware for a single machine. The trade off
is increased complexity in infrastructure and maintenance for the
deployment.

Why Sharding?

• In replication, all writes go to master node

• Latency sensitive queries still go to master

• Single replica set has limitation of 12 nodes

• Memory can\'t be large enough when active dataset is big

• Local disk is not big enough

• Vertical scaling is too expensive

**Sharding Topology**

In MongoDB sharding happens at the collection level. That is to say you
can have a combination of sharded and unsharded collections. Let's look
at a simple example topology.

![](https://github.com/samirsahoo007/SQL/blob/master/images/sharded.png){width="6.688888888888889in"
height="4.459259623797025in"}

The application does not directly talk to the shards but instead talks
through the sharding proxy ***Mongos***. *Mongos* is responsible for the
routing of writes and queries to the shards.

Sharding in MongoDB

![](https://github.com/samirsahoo007/SQL/blob/master/images/sharding2.png){width="6.688888888888889in"
height="4.581889763779528in"}

A MongoDB [sharded
cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)
consists of the following components:

**• Shards** − Shards are used to store data. This is nothing but a
MongoDB instance which holds the subset of the data. In production
environments, all shards need to be part of replica sets.

**• Config Servers** − Config servers store the cluster\'s metadata.
This data contains a mapping of the cluster\'s data set to the shards.
The query router uses this metadata to target operations to specific
shards. In production environment, sharded clusters have exactly 3
config servers. This is a mongodb instance which holds metadata about
the cluster.

**• Query Routers** − Query routers are basically mongo instances,
interface with client applications and direct operations to the
appropriate shard. The query router processes and targets the operations
to shards and then returns results to the clients. A sharded cluster can
contain more than one query router to divide the client request load. A
client sends requests to one query router. Generally, a sharded cluster
have many query routers.

![](https://github.com/samirsahoo007/SQL/blob/master/images/MongoDB-Replica-Sets.jpg){width="6.688888888888889in"
height="5.471511373578303in"}

Replica Set Features

**Shard Keys**

To distribute the documents in a collection, MongoDB
[partitions](https://docs.mongodb.com/manual/reference/glossary/#term-data-partition)
the collection using the [shard
key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key).
The [shard
key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)
consists of an immutable field or fields that exist in every document in
the target collection.

You choose the shard key when sharding a collection. The choice of shard
key cannot be changed after sharding. A sharded collection can have only
*one* shard key. See [Shard Key
Specification](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key-creation).

To shard a non-empty collection, the collection must have an
[index](https://docs.mongodb.com/manual/reference/glossary/#term-index)
that starts with the shard key. For empty collections, MongoDB creates
the index if the collection does not already have an appropriate index
for the specified shard key. See [Shard Key
Indexes](https://docs.mongodb.com/manual/core/sharding-shard-key/#sharding-shard-key-indexes).

The choice of shard key affects the performance, efficiency, and
scalability of a sharded cluster. A cluster with the best possible
hardware and infrastructure can be bottlenecked by the choice of shard
key. The choice of shard key and its backing index can also affect the
sharding strategy that your cluster can use.

See the [shard
key](https://docs.mongodb.com/manual/core/sharding-shard-key/)
documentation for more information.

**Chunks**

MongoDB partitions sharded data into
[chunks](https://docs.mongodb.com/manual/reference/glossary/#term-chunk).
Each chunk has an inclusive lower and exclusive upper range based on the
[shard
key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key).

**Balancer and Even Chunk Distribution**

In an attempt to achieve an even distribution of chunks across all
shards in the cluster, a
[balancer](https://docs.mongodb.com/manual/core/sharding-balancer-administration/)
runs in the background to migrate
[chunks](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)
across the
[shards](https://docs.mongodb.com/manual/reference/glossary/#term-shard)
.

**High Availability**

A [sharded
cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster)
can continue to perform partial read / write operations even if one or
more shards are unavailable. While the subset of data on the unavailable
shards cannot be accessed during the downtime, reads or writes directed
at the available shards can still succeed.

Starting in MongoDB 3.2, you can deploy [config
servers](https://docs.mongodb.com/manual/reference/glossary/#term-config-server)
as [replica
sets](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set).
A sharded cluster with a Config Server Replica Set (CSRS) can continue
to process reads and writes as long as a majority of the replica set is
available.

In version 3.4, MongoDB [[removes support for SCCC config
servers]{.underline}](https://docs.mongodb.com/manual/release-notes/3.4-compatibility/#compat-remove-sccc).

In production environments, individual shards should be deployed as
[replica
sets](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set),
providing increased redundancy and availability.

Careful consideration in choosing the shard key is necessary for
ensuring cluster performance and efficiency. You cannot change the shard
key after sharding, nor can you unshard a sharded collection.

Sharded and Non-Sharded Collections¶

A database can have a mixture of sharded and unsharded collections.
Sharded collections are
[partitioned](https://docs.mongodb.com/manual/reference/glossary/#term-data-partition)
and distributed across the
[shards](https://docs.mongodb.com/manual/reference/glossary/#term-shard)
in the cluster. Unsharded collections are stored on a [primary
shard](https://docs.mongodb.com/manual/reference/glossary/#term-primary-shard).
Each database has its own primary shard.

Connecting to a Sharded Cluster¶

You must connect to a
[mongos](https://docs.mongodb.com/manual/reference/glossary/#term-mongos)
router to interact with any collection in the [sharded
cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster).
This includes sharded *and* unsharded collections. Clients should
*never* connect to a single shard in order to perform read or write
operations.

You can connect to a
[mongos](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)
the same way you connect to a
[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod),
such as via the
[mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo)
shell or a MongoDB
[driver](https://docs.mongodb.com/ecosystem/drivers?jump=docs).

Sharding Strategy¶

MongoDB supports two sharding strategies for distributing data across
[sharded
clusters](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster).

Hashed Sharding¶

Hashed Sharding involves computing a hash of the shard key field's
value. Each
[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)
is then assigned a range based on the hashed shard key values.

TIP

MongoDB automatically computes the hashes when resolving queries using
hashed indexes. Applications do not need to compute hashes.

While a range of shard keys may be "close", their hashed values are
unlikely to be on the same
[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk).
Data distribution based on hashed values facilitates more even data
distribution, especially in data sets where the shard key changes
[monotonically](https://docs.mongodb.com/manual/core/sharding-shard-key/#shard-key-monotonic).

However, hashed distribution means that ranged-based queries on the
shard key are less likely to target a single shard, resulting in more
cluster wide [broadcast
operations](https://docs.mongodb.com/manual/core/sharded-cluster-query-router/#sharding-mongos-broadcast)

See [Hashed
Sharding](https://docs.mongodb.com/manual/core/hashed-sharding/) for
more information.

Ranged Sharding¶

Ranged sharding involves dividing data into ranges based on the shard
key values. Each
[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)
is then assigned a range based on the shard key values.

A range of shard keys whose values are "close" are more likely to reside
on the same
[chunk](https://docs.mongodb.com/manual/reference/glossary/#term-chunk).
This allows for [targeted
operations](https://docs.mongodb.com/manual/core/sharded-cluster-query-router/#sharding-mongos-targeted)
as a
[mongos](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)
can route the operations to only the shards that contain the required
data.

The efficiency of ranged sharding depends on the shard key chosen.
Poorly considered shard keys can result in uneven distribution of data,
which can negate some benefits of sharding or can cause performance
bottlenecks. See [shard key
selection](https://docs.mongodb.com/manual/core/ranged-sharding/#sharding-ranged-shard-key)
for ranged sharding.

See [Ranged
Sharding](https://docs.mongodb.com/manual/core/ranged-sharding/) for
more information.

Zones in Sharded Clusters¶

In sharded clusters, you can create
[zones](https://docs.mongodb.com/manual/reference/glossary/#term-zone)
of sharded data based on the [shard
key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key).
You can associate each zone with one or more shards in the cluster. A
shard can associate with any number of zones. In a balanced cluster,
MongoDB migrates
[chunks](https://docs.mongodb.com/manual/reference/glossary/#term-chunk)
covered by a zone only to those shards associated with the zone.

Each zone covers one or more ranges of [shard
key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)
values. Each range a zone covers is always inclusive of its lower
boundary and exclusive of its upper boundary.

You must use fields contained in the [shard
key](https://docs.mongodb.com/manual/reference/glossary/#term-shard-key)
when defining a new range for a zone to cover. If using a
[compound](https://docs.mongodb.com/manual/reference/glossary/#term-compound-index)
shard key, the range must include the prefix of the shard key. See
[shard keys in
zones](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding-shard-key)
for more information.

When choosing a shard key, carefully consider the possibility of using
zone sharding in the future, as you cannot change the shard key after
sharding the collection.

Most commonly, zones serve to improve the locality of data for sharded
clusters that span multiple data centers.

TIP

Starting in MongoDB 4.0.3, setting up zones and zone ranges *before* you
shard an empty or a non-existing collection allows for a faster setup of
zoned sharding.

See
[zones](https://docs.mongodb.com/manual/core/zone-sharding/#zone-sharding)
for more information.

Collations in Sharding¶

Use the
[shardCollection](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection)
command with the collation : { locale : \"simple\" } option to shard a
collection which has a [default
collation](https://docs.mongodb.com/manual/reference/collation/).
Successful sharding requires that:

• The collection must have an index whose prefix is the shard key

• The index must have the collation { locale: \"simple\" }

When creating new collections with a collation, ensure these conditions
are met prior to sharding the collection.

NOTE

Queries on the sharded collection continue to use the default collation
configured for the collection. To use the shard key index's simple
collation, specify {locale : \"simple\"} in the query's [collation
document](https://docs.mongodb.com/manual/reference/collation/).

See
[shardCollection](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection)
for more information about sharding and collation.

Change Streams¶

Starting in MongoDB 3.6, [change
streams](https://docs.mongodb.com/manual/changeStreams/) are available
for replica sets and sharded clusters. Change streams allow applications
to access real-time data changes without the complexity and risk of
tailing the oplog. Applications can use change streams to subscribe to
all data changes on a collection or collections.

**When to Shard**

One of the typical errors people commit is to shard too early. The
reason this can be a problem, is that sharding requires the developer to
pick a shard key for distribution of the writes and it's easy to pick
the wrong key early on due to not knowing how the data will be accessed
over time.

This can lead reads to be inefficiently spread out across shards causing
unnecessary IO and CPU usage in order to retrieve the data. Once the
collection is sharded, it can be very time consuming to undo it, as all
the data will have to be migrated from one sharded collection to another
by reinserting all the documents into the new collection.

That said, let's look at some reason you might want to Shard.

1 Your Working Set no longer fits in the memory of your server. In this
case, sharding can help make more of your Working Set stay in memory by
pooling the RAM of all the shards. If you have a 20GB Working Set on a
16GB machine, sharding can split this across 2 machines for a total of
32GB of RAM available, potentially keeping all of the data in RAM and
avoiding disk IO.

2 Scaling the write IO. You need to perform more write operations than
what a single server can handle. By Sharding you can scale out the
writes across multiple computers, increasing the total write throughput.

**Choosing a Shard Key**

It's important to pick a Shard key based on the actual read/write
profile of your application to avoid inefficient queries and write
patterns. There are a couple of tips that can help in the quest to
identify the right shard key.

Easily Divisible Shard Key (Cardinality)

When discussing cardinality in the context of MongoDB, we refer to the
ability to partition data into chunks distributed across all the shards.
Let's look at some example shard keys.

Picking the **state** field as a shard key

In our documents the state field contains the US state for a specific
address. This field is considered low cardinality, as all documents
containing the same state will have to reside on the same shard. Since
the key space for states is a limited set of values, MongoDB might end
up distributing the data unevenly across a small set of fixed chunks.
This might cause some unintentional side effects.

• It could lead to un-splittable chunks that could cause migration
delays and unnecessary IO due to MongoDB attempting to split and migrate
chunks.

• The un-splittable chunks might make it difficult to keep the shards
balanced,causing load distribution to be uneven across the shards.

• If the number of values map to a maximum of un-splittable chunks,
adding more shards might not help as there are fewer chunks available
than shards. Due to the chunks being un-splittable, we cannot leverage
the additional shards.

Picking the **post code** field as a shard key

If we pick the post code field as a shard key, the number of possible
post codes is higher than when using the state state field. So the shard
key is considered to have a higher cardinality than when using the state
field. However the number of post codes does not completely mitigate the
situation of un-splittable chunks. As one can imagine, one post code has
a lot more associated addresses, which could make that specific chunk
un-splittable.

Picking the **phone number** field as a shard key

The phone number field has high cardinality as there might be few users
mapping to a specific phone number. This will make it easy for MongoDB
to split chunks, as chunks won't be made up of documents that map to the
same shard key, avoiding the possible problem of using the state field.

TIP

Cardinality

Always consider the number of values your shard key can express. A
sharding key that has only 50 possible values, is considered low
cardinality, while one that might be able to express several million
values might be considered a high cardinality key. High cardinality keys
are preferable to low cardinality keys to avoid un-splittable chunks.

High Randomness Shard Key (Write Scaling)

A key with high randomness will evenly distribute the writes and reads
across all the shards. This works well if documents are self contained
entities such as Users. However queries for ranges of documents, such as
all users with ages less than 35 years will require a scatter gather
query where all shards are queried and a merge sort is done on Mongos.

Single Shard Targeted Key (Query Isolation)

Picking a shard key, that groups the documents together will make most
of the queries go to a specific Shard. This can avoid scatter gather
queries. One possible example might be a Geo application for the UK,
where the first part of the key includes the postcode and the second is
the address. Due to the first part of the shard key being the postcode,
all documents for that particular sort key will end up on the same
Shard, meaning all queries for a specific postcode will be routed to a
single Shard.

The UK postcode works as it has a lot of possible values due to the
resolution of postcodes in the UK. This means there will only be a
limited amount of documents in each chunk for a specific postcode.
However, if we were to do this for a US postcode we might find that each
postcode includes a lot of addresses causing the chunks to be hard to
split into new ranges. The effect is that MongoDB is less able to spread
out the documents and in the end this impacts performance.

Routing Shard Keys

Depending on your Shard key the routing will work differently. This is
important to keep in mind as it will impact performance.

  ---------------------------------------- -------------------------
  Type Of Operation                        Query Topology
  Insert                                   Must have the Shard key
  Update                                   Can have the Shard key
  Query with Shard Key                     Routed to nodes
  Query without Shard Key                  Scatter gather
  Indexed/Sorted Query with Shard Key      Routed in order
  Indexed/Sorted Query without Shard Key   Distributed sort merge
  ---------------------------------------- -------------------------

Inbox Example

Imagine a social Inbox. In this case we have two main goals

1 Send new messages to it's recipients efficiently

2 Read the Inbox efficiently

We want to ensure we meet two specific goals. The first one is to write
to multiple recipients on separate shards thus leveraging the write
scalability. For for a user to read their email box, one wants to read
from a single shard avoid scatter/gather queries.

![](https://github.com/samirsahoo007/SQL/blob/master/images/sharded_inbox_write.png){width="6.688888888888889in"
height="3.8238156167979in"}

How does one go about getting the correct shard key? Let's assume we
have two collections inbox and users, in our social database.

**var** db = db.getSisterDB(**\'social\'**);

db.shardCollection(**\'social.inbox\'**, {owner: 1, sequence: 1});

db.shardCollection(**\'social.users\'**, {user\_name: 1});

*Example 1: Shard the social collections*

Let's write and read to the collections with some test data to show how
we can leverage the sharding.

**var** db = db.getSisterDB(**\'social\'**);

**var** msg = {

from: **\'Christian\'**,

to: \[**\'Peter\'**, **\'Paul\'**\],

sent\_on: **new** **Date**(),

message: **\'Hello world\'**

}

**for**(**var** i = 0; i \< msg.to.length; i++) {

**var** result = db.users.findAndModify({

query: { user\_name: msg.to\[i\] },

update: { **\'\$inc\'**: {msg\_count: 1} },

upsert: **true**,

**new**: **true**

})

**var** count = result.msg\_count;

**var** sequence\_number = **Math**.floor(count/50);

db.inbox.update({ owner: msg.to\[i\], sequence: sequence} ),

{ \$push: {messages: msg} },

{ upsert:**true** });

}

db.inbox.find({owner: **\'Peter\'**})

.sort({sequence: -1})

.limit(2);

*Example 2: Write Email, Read Inbox*

The first part delivers the message to all its recipients. First, it
updates the message count for the recipient and then pushes the message
to the recipient's mailbox (which is an embedded document). The
combination of the Shard key being {owner: 1, sequence: 1}, means that
all new messages get written to the same chunk for a specific owner. The
Math.floor(count/50) generation will split up the inbox into buckets of
50 messages in each.

This last aspect means that the read will be routed directly to a single
chunk on a single Shard, avoiding scatter/gather and speeding up
retrieval.

Multiple Identities Example

What if we need to lookup documents by multiple different identities
like a user name, or an email address?

Take the following document:

**var** db = db.getSisterDB(**\'users\'**);

db.users.insert({

\_id: **\'peter\'**,

email: **\'peter\@example.com\'**

})

*Figure: Sample document*

If we shard by \_id, it means that only \_id queries will be routed
directly to the right shard. If we wish to query by email we have to
perform a scatter/gather query.

There is a possible solution called document per identity. Let's look at
a different way of representing the information.

**var** db = db.getSisterDB(**\'users\'**);

db.identities.ensureIndex({ identifier: 1 }, { unique: **true** });

db.identities.insert({

identifier: {user: **\'peter\'**}, id: **\'peter\'**});

db.identities.insert({

identifier: {email: **\'peter\@example.com\'**, user: **\'peter\'**},

id: **\'peter\'**});

db.shardCollection(**\'users.identities\'**, {identifier: 1});

db.users.ensureIndex({ \_id: 1}, { unique: **true** });

db.shardCollection(**\'users.users\'**. { \_id: 1});

*Example 3: Set up Identities*

We create a unique index for the identities table to ensure we cannot
map two entries into the same identity space. Using the new compound
shard key we can retrieve a user by its email by performing two queries.
Let's see how we can look up the user document of the user with the
email address of peter\@example.com.

**var** db = db.getSisterDB(**\'users\'**);

**var** identity = db.identities.findOne({

identifier: {

email: **\'peter\@example.com\'**}});

**var** user = db.users.find({ \_id: identity.id });

*Example 4: Set up Identities*

The first query locates the identity using the email, which is a routed
query to a single shard. The second query uses the returned identitiy.id
field to retrieve the user by the shard key.

Sharding Anti-Patterns

There are a couple of sharding anti-patterns that you should keep in
mind to avoid some of the more common pitfalls.

Monotonically increasing shard key

A monotonically increasing shard key, is an increasing function such as
a counter or an ObjectId. When writing documents to a sharded system
using an incrementing counter as it's shard key, all documents will be
written to the same shard and chunk until MongoDB splits the chunk and
attempts to migrate it to a different shard. There are two possible
simple solutions to avoid this issue.

Hashing the shard key

From MongodDB 2.4, we can tell MongoDB to automatically hash all of the
shard key values. If we wanted to create documents where the shard key
is \_id with an ObjectId, we could shard the collection using the
following options.

sh.shardCollection( **\"users.users\"**, { \_id: **\"hashed\"** } )

*Example 5: Shard using hashed \_id*

Pre-split the chunks

The second alternative is to pre-split the shard key ranges and migrate
the chunks manually, ensuring that writes will be distributed. Say you
wanted to use a date timestamp as a shard key. Everyday you set up a new
sharded system to collect data and then crunch the data into aggregated
numbers before throwing away the original data.

We could pre-split the key range so each chunk represented a single
minute of data.

/ first **switch** to the data DB

use data;

// now enable sharding for the data DB

sh.enableSharding(**\"data\"**);

// enable sharding on the relevant collection

sh.shardCollection(**\"data.data\"**, {**\"\_id\"** : 1});

// Disable the balancer

sh.stopBalancer();

*Example 6: Shard using \_id field*

Let's split the data by minutes.

/ first **switch** to the admin db

use admin;

// 60 minutes in one hour, 24 hours in a day

**var** numberOfMinutesInDay = 60\*24;

**var** date = **new** **Date**();

date.setHours(0);

date.setMinutes(0)

date.setSeconds(0);

// Pre-split the collection

**for**(**var** i = 0; i \< numberOfMinutesInDay; i++) {

db.runCommand({ split: **\"data.data\"**,

middle: {\_id: date}

});

date.setMinutes(date.getMinutes() + 1);

}

*Example 7: Pre-split*

Finally re-enable the balancer and allow MongoDB to start balancing the
chunks to the shards.

// Enable the balancer

sh.startBalancer();

*Example 8: Enable balancer*

You can monitor the migrations by connecting to mongos, using the mongo
shell and executing the sh.status() command helper to view the current
status of the sharded system.

The goal of pre-splitting the ranges is to ensure the writes are
distributed across all the shards even though we are using a
monotonically increasing number.

mongos for "MongoDB Shard," is a routing service for MongoDB shard
configurations that processes queries from the application layer, and
determines the location of this data in the [sharded
cluster](https://docs.mongodb.com/manual/reference/glossary/#term-sharded-cluster),
in order to complete these operations. From the perspective of the
application, a mongos instance behaves identically to any other MongoDB
instance.

Step by Step Sharding Cluster Example

Step 1) Create a separate database for the config server.

mkdir /data/configdb

Step 2) Start the mongodb instance in configuration mode. Suppose if we
have a server named Server D which would be our configuration server, we
would need to run the below command to configure the server as a
configuration server.

mongod --configdb ServerD: 27019

Step 3) Start the mongos instance by specifying the configuration server

mongos --configdb ServerD: 27019

Step 4) From the mongo shell connect to the mongo\'s instance

mongo --host ServerD --port 27017

Step 5) If you have Server A and Server B which needs to be added to the
cluster, issue the below commands

sh.addShard(\"ServerA:27017\")

sh.addShard(\"ServerB:27017\")

Step 6) Enable sharding for the database. So if we need to shard the
Employeedb database, issue the below command

sh.enableSharding(Employeedb)

Step 7) Enable sharding for the collection. So if we need to shard the
Employee collection, issue the below command

Sh.shardCollection(\"db.Employee\" , { \"Employeeid\" : 1 ,
\"EmployeeName\" : 1})

Summary:

• As explained in tutorial, Sharding is a concept in MongoDB, which
splits large data sets into small data sets across multiple MongoDB
instances.
