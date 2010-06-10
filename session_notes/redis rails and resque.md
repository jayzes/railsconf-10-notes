# Redis, Rails and Resque # 
## 3 favorites pieces of software ##
* Redis - "we've been wanting it for a long time, even if we didn't know that we wanted it"
	* Key-value store for data structures
	* Hash, list, set, sorted set, string, integer, all with different operations
	* RAM used to be a limiting facter, not anymore.  Can shard across machines to increase capacity
	* Used @ GH as a routing table, monitoring tool for file server load, etc.
	* Can use in multiple different languages, which is huge
	* antirez is awesome
		* Responsive and transparent (blog posts, etc.)
		* Included client libraries and CLI utils for easy setup
* Unicorn - not a stupid name
	* Uses Mongrel parser (which is awesome)
	* May not be the fastest, but it's uber reliable and graceful at dealing with failure
	* Uses all kinds of UNIXy underpinnings
	* Unicorn uses select() to load balance to workers without blocking
	* Preforking worker to get shared memory, fast startup, and durability for stalled workers
		* No problems with all mongrels being in the restarting state anymore
		* Rack-based, but using a shim for Rails 2.2
		* Luke Malia's GC middleware <-- CHECK OUT
	* Great maintainer, very attentive, solid code
* Delayed_job - background processor, queue, man about town?
	* Stupid simple
	* Sort of like a post-commit hook for your application actions
	* GH has gone through 6-7 queueing systems
		* SQS was first - basic wrapper with loop/do
		* DJ doesn't deal with chaos in workers very well, unfortunately - hanging jobs, etc.
* Time to roll your own
	* Not based on SQL cause we're not good at it
	* Needs to be easy!
	* Needs to be atomic for operations to ensure no work is being done multiple times
	* High visibility - jump in, kill jobs, etc.
	* High relaibility for the system (at the cost of reliability for individual jobs)
	* Didn't want full serialization YAML code from DJ - too much overhead
	* Note: Please don't write push-bots
	* Enter resque!
		* Based on redis
		* No complicated priorities - queues for buckets
			* Makes it easy to use redis-cli to investigate things
		* JSON serialization makes things very simple - class and args, that's it
		* You can have workers in many different languages - Node, C, etc.
		* Very simple API
		* Doesn't actually implement queueing - lets redis do that cause it's a better implementation
		 	* Frontend queueset - critical, high, low, etc.
			* Queue names can indicate locality - different queues for pages, frontend, backend, etc.
				* Pages are static data through nginx
		* Easy monitoring - just look at redis keys
		* Preforking model
			* Child can have chaos, it's okay, just kill it
			* Master can deal with signals and manage the whole deal through its own signals
		* Abuse the procline to help with debugging
		* Web UI
		* Trying to make it an awesome open source project
			* Open list, community of patches, etc.
			* Balance the README so it isn't too long
			* Need to have a vision and a plan for your OSS projects, and be available - that will bring people running
			* Have no features in the main project - encourage a healthy ecosystem for plugins to support functionality you don't want as a maintainer
		* What about resque 2.0?
			* BLPOP in Redis 2 to eliminate polling between client/messaging server
			* Can do multiple keys - almost a full queueing system with no Ruby code!
			* More plugins and hooks - nicer plugin API and demo app
			* Redoing UI in Mustache
			
## Q&A ##
* Use supervise for daemon monitoring
* Actually run background tasks on the frontend because Unicorn is so efficient