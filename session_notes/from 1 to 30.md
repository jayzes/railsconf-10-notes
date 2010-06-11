# From 1 to 30 - Breaking down a big app

## Tale of Two Buildings
* Forbidden city - constantly refactored and made bigger, lasted for years and years
* Lotus Revierside highrise - built bigger and bigger by greedy developers, fell over on its side (http://www.q2hoo.com/2009/06/a-series-of-photos-about-collapsed-shanghai-flat-lotus-riverside-compound.htm)

## The app is a distributed call center app for language trainers
* Mission critical - kept adding more and more and more to it

## Monolithic - entire app runs as one Rails application
* Confuses and scares new staff <-- BIG PROBLEM
* Hard to test/extend/scale
* Code gets messy

## What is 30?
* An ecosystem of independent Rails applications
* Linked and seamless
* Users don't see the difference

## Each app:
* Has a separate database
* Runs independently (has its own user stories)
* Is lightweight (can be handled by a single developer)
* Has tight internal cohesion and loose external coupling
* Advantages:
	* Independent development cycles
	* Developer autonomy
	* Safety from technology (im)maturity
	* Appeal to developer laziness - overriding philosophy of making it easier on yourself (cause it's going to happen anyway)

## The Mysteries of the Forbidden City
* Consistent UI/UX
	* Shared CSS/JS framework and style guide for all apps
	* Gem all common helpers (combo search, list tables w/ pagination)
	* Any time you see repetition, extract and reuse!
		* Implement new view code in a separate application
		* Extract into plugin
		* Roll up into a gem
* Sharing data
	* For example, purchase app needs data stored in the 'courses' application
		* Tried built-in Rails stuff (ActiveResource) - tends to be really slow and difficult to debug
		* Lose Rails magic
		* Tried SVN externals, but a pain to manage
	* Read-only database connections
		* One app responsible for writing/updating/creating data, the others just reading
		* Pretty safe way of integrating
		* Acts_as_readonly macro to disable AR save functionality
		* Configuration is hell, however
			* Use CoreService ActiveResource model to retrieve app-specific info
			* When each app starts, it posts its configuration info to a central service for all others to access 
			* Ordering issues aren't too bad - not too many tight dependencies
	* Services - pain to build but necessary for write-centric operations
	* AJAX-loaded view segments - put in a div from another apps URL
* Authentication and Access Control
	* Registration/Login
		* Share sessions between applications via a common session store
	* Profile management
	* Role-based Access Control
		* Controller-based - each controller is a node
		* Posted to CoreService when app starts
		* Retrieve access rights
		* Controllers are responsible for checking rights
* Services
	* File uploads - can't use S3 because of the Chinese firewall so wrote a similar service to receive files posted in the background
		* make use of super-polymorphic relationships keyed off the application as well as model and id
	* Mail - universal sending
	* Comet service
* Deployment
	* Load each app into a subdirectory, then use unicorn with multiple paths
	* Use NGinx as a reverse proxy to rewrite URIs to be uniform
	* Definitely extra memory consumption, but it's worth the dev time savings
	* Have to scale apps independently based on traffic levels - very easy to tweak and balance
	
## Net result: Higher productivity
* Faster build-to-deploy
* More developer autonomy
* Safer
* more scalable
* Easier for new folks to jump in
* Greater developer happiness