# Million Dollar Mongo - Breaking Free of the Relational Mindset
## Backstory ##
* Client facing total business failure - 2 years of dev, little to no progress, had a hard 6-month deadline
* Competitive RFP
* In the process of signing a very large insurance company for a SAAS app that they didn't have built
	* Service almost 1/2 of insured people in the US
* Started with a $500k budget, scope ballooned after 4mo, eventually billed $1.7MM, over 10,000 hours in 9 months
	* Peak head count was 14 people across 4 continents
	* Reqs driven by client's client - dealing with 1 record at a time since it's pharmaceutical/healthcare
	* Production releases Feb to April 2010
	* Ridiculous project points breakdown graph showing scope creep - could be useful for factory!
* Technical backstory
	* Originally relational data, stored across hundreds of tables
	* Ridiculously join-heavy, query-heavy (35-36 per request)
	* Original system was on DotNetNuke and MSSQL
## The Solution ##
* At core, a patient record system
* Files are very distinct and have no overlap.
* Built spikes for Mongo, Couch, and Cassandra
	* Perf numbers on Twitter
	* Big selling point for Mongo was dynamic queries
		* Couch Map/Reduce felt too much like stored procs
		* Couch fell over a bit at 10,000 docs on insert/update operations
		* Cassandra had "too much fucking XML"
	* Schemaless approach made legacy migration much easier
		* CSRs needed to build/update records with history
		* Sotred history by generating new keys on the fly
* Resistance to change was a problem
	* Hashrocket didn't have an internal consensus that this was the right way to go - Mongo was only 0.8, was this the right thing to stake such a large engagement on?
	* "So, are we going to have to write our own ORM for this thing?"
	* Out of the normal comfort zone - both good and bad
		* Normal Rails development has become "more like a zombie"
		* New challenges, very interesting
* Easiest DB to install ever - no deps
* Schemaless DB makes things uber-agile because migrations are painless
	* Able to cut down time for data import process
	* Code has to be able to deal with different data structures - more app-side load
	* Client DBAs kept changing the schema, causing issues
	* (fast ramp-up for non-idiots) - if you can understand JSON, you can work with mongo!
* Mongomapper -> Mongoid
	* Hashrocket fork had 33 patch releases, had more that didn't really fit into the MM philosophy
	* Prefers embedding documents for everything
	* Default to atomic operations
	* Rich criteria API, master/slave support, versioning, etc.
	* Is NOT ActiveRecord (!!!)
	* Designed with performance in mind
	* Average document size is 500k, biggest about 1MB (4MB is limit, but would be hard to hit) - big but can get everything in 1 request
	* As of Mongo 1.4, you can index asynchronously on inserts
	* Regex queries on fields, etc.
* Schema design patterns
	* Denomalization is acceptable
	* Embed whatever you can
	* Be wary of pre-epoch dates - use integers where you can
	* One query to rule them all - no joins!
* MySQL is fast, Mongo is faster
	* 300,000 writes/second
	* Didn't even need to shard mongo on an EC2 XL instance
* Hybrid DB model
	* Hierarchical data in Mongo, relational data in MyISAM, transactions in InnoDB, simple data in Redis
	* Having more than one DB is okay - favor suitability over simplicity
	* Needed relational DB as staging tables for normalized imports
	* Working without transactions
		* Atomic updates mostly cover you
		* Optimistic locking is pretty easy to roll in
		* Integration tests require manual rollback
		* Selenium tests can run in a separate process (neat) - enables something like SpecJour helps the 40-minute cucumber run
* In Production
	* Deployed on EC2
	* Close to 500GB of data
	* Put Mongo on its own utility instance instead of the DB master since MySQL will fight it
	* Feed it lots of RAM and disk space
	* Setting up chef scripts was pretty easy
	* Costs about $10k a month
	* For backups, do a mongo dump and send it to S3
		
## What happens when 'real america' interferes? ##
* Relational DBAs can be retarded (especially when threatened by change)
	* Set up squealer to provide a proxy to MySQL for ad-hoc querying and a GUI
* How the hell did we end up in bed with these people?
* Emerging technologies threatened those opposed to change
	* Anyone with a C and O in their title has no business speaking about technology
	* CIO saw the well-suited/less-well-suited page and pitched a fit
	* Called up another Rails firm (Intridea) to verify, that firm backstabbed somewhat and said they didn't think Mongo was an appropriate choice, and offered to convert in a month
	* Real bummer in the community that this sort of stuff happens

* Would be nice if more case studies on large mongo apps existed for background info.