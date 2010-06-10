# Don't repeat yourself, repeat others #
* Experiences writing Ruby - improve, learn, share
* 3 points - create, steal, think

## Create ##
* What we have to learn to do, we learn by doing - Aristotle
* Reinvent the wheel - important to dev education and skill (like bodybuilder)
* MongoMapper - redid others work, but gained an appreciation for the work of others (DM, AR)
* Dynamic languages are dynamic
	* autoload - classes loading on demand
	* method_missing magic
	* dirty keys in AR
	* include hook and class_eval - used for the mapper mixins
	* super and the ancestor tree (with overrides in the same file)
	* dynamically defined classes and modules (Class.new, Module.new, etc)
* Objects can do more than #new and #save
	* equality - #eql? versus == (can alias == to #eql?) but don't touch #equal
	* clone vs. dup
		* clone doesn't clone internal objects, like hashes
		* can use initialize_copy method definition for your class to work properly
	* Hooks
		* inherited hook - this is how STI works
	* Validations, callbacks, comparable, enumerable, etc. 
* Patterns - not just for the enterprise
	* Proxies a la AR - class as an interface to another class
	* Decorators - adding behavior to an instance dynamically.  Can use extend/include on an object!
	* Identity map - "a man with 2 watches never knows the time"
		* Don't ever load the same object 2x
* Local APIs - eat your own dogfood to make sure it's consistent for others
* "I want to make things, not just glue things together" - Mike Taylor
	
## Steal ##
* HTTParty steals from DM, AR
* Happymapper steals a *ton* from DM
* Mongomapper - steals from sequel
* Plucky is a lot like Hash, Arel
* gem whois
* canable - thanks pivotal

## Think ##
* Make decisions.  Don't add things because you *might* need them
* Extraction vs. prediction.  Don't extract until you repeat yourself.
* Refactor (read the book)
	* Sketch them out on paper
* Write
	* Helps you connect all the dots on what you've learned
	* Write for yourself, helping others is incidental