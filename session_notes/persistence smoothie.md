# Persistence Smoothie #
* Example code is at http://www.github.com/flipsasser/Persistence-Smoothie

## What is NoSQL? ##
* Any kind of persistene
* Generally speaking, ACID is out the door in favor of speed and scalability
* Probably shouldn't be running it *all* the time - good tool for the toolbox though
* Key-value stores
	* Redis, riak, Voldemort, Tokyo Cabinet, MemcacheDB
	* Hashes in the sky
	* Use case is taking ugly (and non-performant) joins and transforming them into denormalized data
* Document Stores
	* Mongo, Couch, Riak
	* Multi-level hashes
	* Generally query with map/reduce to flatten
	* Use case is loose schema, no ACID needed
* Column-based stores
	* Cassandra,  HBase
	* Have a schema, more like a traditional DB
* Graph stores
	* Track relationships efficiently
	* Neo4j, HypergraphDB, InfoGrid
	* Use case is deep relationship mapping

## How do you get started with an existing app written with SQL? ##
* First option is putting together helpers - just slam them into models
	* not very DRY
* Second option is using datamapper
	* swiss army knife of ORMs
	* much simpler, gives a lot of AR niceties
	* still requires AR refactoring, ecosystem not as mature
	
## Example - Convert a Store App ##
* Cases are Authentication, Products, Purchases, Activity stream
	* Auth is kind of not worth converting
	* Products are the sweet spot though - loose schema, lots of different structure
	* Purchases need to be transactional, don't mess with those
	* Activity stream is another sweet spot - lots of joins to be eliminated (!)
* (Live coding)
	* Can use OpenStruct as a base for some simple persistence layers
* Deployment and data portability become a much bigger pain in the ass when dealing with multiple data stores
* The ecosystem of tools around ActiveRecord is really nice, but you basically have to say goodbye to it for many NoSQL ORMs (gem uninstall everything)