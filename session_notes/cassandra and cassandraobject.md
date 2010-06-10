# Cassandra and CassandraObject #

### Disclaimer: I (Koz) don't have to scale, so no scalability porn here ###

## About Cassandra ##
* Pre-1.0, full-fledged Apache project
* Distributed and fault-tolerant and elastic from the ground up
* Runs on a cluster of servers that organize into a ring based on key ranges
	* Nodes are aware of each other and talk using a gossip-based protocol
	* Configurable keyspace partitioning - random (MD5, evenly-distributed) or order preserving (solely key-based, uneven)
	* Configurable replication factor - number of nodes to write items of data to.  Defaults to 3
	* Replication happens asynchronously after writes - this is where consistency levels come into place
		* Essentially, how many nodes need to have responded to a request in order for it to be considered consistent (and thus complete)
		* Options include none, one, quorum (majority), all
		* Allows you to trade off performance and data consistency speed
	* Fault tolerance will automatically skip dead servers when reading/writing data - can decommission nodes and reassign data on the fly
	* Adding a new node will automatically rebalance the cluster and increase capacity accordingly

## Data Model ##
* Fundamentally a column store (using keys) - not a key-value store!
* Columns consist of a name, value, and a timestamp
* Column families/rows consist of an ordered set of columns
* Supercolumns point to a set of rows (a 2 level hash, essentially, like JSON)
* Can set ordering on columns families using CompareWith (for example, TimeUUID)
* There's no WHERE or ORDER or COUNT or SUM in querying - it's more about query-driven modelling
	* Populate a model that lets you query what you need
	* Create column families like indexes to search - you need a column family per query, essentially
	* Can get specific keys individually or grab ranges (only feasible with the order-preserving partitioner due to assembly time)
	
## Do you really need Cassandra? ##
* Different enough that you want to make sure you need it before you dive in
* Won't be writing a little blog in it, likely

## CassandraObject ##
* Opinionated, hierarchical like ActiveRecord
* Started out as a way to prove the usefulness of ActiveModel (and learn Cassandra) - not to solve scaling problems
	* Mostly AR-compatible
* Usage
	* Define attributes and types (pluggable system for implementing those in Cassandra)
	* Validations (exactly like AR, except the stuff reliant on DB ops like uniqueness)
	* Keys - selection is crucially important since it determines how you can query
		* Can do UUID, natural (based on an attribute), or some other key factory object (like a Redis INCR-based factory, very simple API)
	* Migrations
		* CassandraObject uses an integer schema version attribute to auto-execute data migrations on access
		* Pretty cool and transparent
	* Indexes
		* Lets you do AR_style find_by_x methods
		* Can be unique or non-unique
		* Read-repair is used to re-update indexes on operations
	* Associations
		* Deliberately named differently since they function quite differently than AR - no proxy create methods or finders, etc.
* The library is still very beta and API/data is in flux, but Koz is happy to take patches
* Works on Rails 2.x and 3