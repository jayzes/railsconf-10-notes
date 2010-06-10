# Scaling to Millions
* EC2 performance is definitely an issue - big difference between physical and virtual hardware
* Mongo falls down on big datasets/high concurrency due to locking issues
* Cassandra is cool, but likes larger clusters
	* Recovery is very resource intensive, can kill smaller machines
	* Worth the hassle if you have billions of rows.  
* Lesson: I'm a lousy sysadmin
	* Don't drink the cool aid or believe the hype, definitely do your own homework before going with a new piece of infrastructure