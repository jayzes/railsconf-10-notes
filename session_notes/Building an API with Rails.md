# Building an API with Rails #
* Joe Ferriss (Thoughtbot/Hoptoad), Jeremy Kemper (37S), Marcel Molina (Twitter), Rick Olson (Github/ENTP), Derek Willis (NYT)

## Authentication ##
* Twitter implemented OAuth 1.0 about a year ago, then 1.0a for a security issue
	* 1.0 spec is difficult to implement because of the signature algorithm - makes requests difficult and easy to screw up
	* 2.0 spec uses tokens over SSL instead - much simpler.  
* 37S is working on OAuth
	* Distinction between authentication and authorization is interesting - changes app responsibilities
	* Wrap is an interesting concept - rolled into OAuth 2
* Github using OAuth 2
	* Lets you compartmentalize access
* Thoughtbot
	* Simplicity is key - fast setup
	* OAuth is cool but overkill/unnecessary
	
## Input and Response Formats ##
* Thoughtbot
	* Originally accepted multiple formats, simplifying and standardizing helped scale more easily (write-heavy)
	* Documentation is huge - XML schemas provide this implicitly which is nice
* NYT
	* Read-heavy API, necessary to offer larger data streams and archives in multiple formats for analysis
* Twitter
	* Like to give devs many options
	* Moving to standardize on JSON - don't see the lack of choice as an issue
* 37S
	* Treat all formats equally, restful/resourceful architecture is huge and makes a lot of this a non-issue
	* Smaller apps make it much easier
	
## Versioning ##
* Twitter
	* Really hard problem (and great interview question) - no real good answers yet
	* 5 separate things to deal with - format, params, serialization, features, etc.
	* Fork app and do separate deploys vs. inheritance - change management is a bitch
	* Twitter has held off - no compelling force driving to implement, don't want to inconvenience devs.  Have handled issues by other means.
	* Build in new functionality on a switch on/switch off basis - can ease transitions.  Just make sure you clean them up later.
* 37S
	* Easy to get drunk on engineering and want to tackle it when it's not really a problem
	* Give developers lead time, but don't sweat making changes
	* Flip formats with mime types, etc - lots of little tricks to ease transitions
* Thoughtbot
	* Some people will never upgrade until things are broken, but at the same time the dropoff/abandonment rate is not very big (for those that don't make the jump)
* NYT
	* Developers are flexible and will roll with a lot of things.  Don't abuse them, but don't bend over backwards either.
	
## Scaling ##
* NYT uses mashery for authentication but not for caching - on a per-app basis
	* Use varnish for frontend - totally awesome
	* Internal use is huge - timing and cache invalidation is critical to the usefulness of the dataset
* Hoptoad is difficult because of the write-heavy nature
	* Everything has a cost - need to figure out what they are and prioritize accordingly
	* Rate limits, etc. help
* 37S
	* API traffic is way different to cache than web traffic - lots of scraping, batching, etc.  Need different strategies.
	* Need to rate limit to keep it under control.  Clients get accidentally out of control often.
* Twitter caches everything, but you need to watch out for working set size.  Be smart about it.
	* Dynamic assembly at runtime
	* Mind the cost of expiration too
	* Streaming API for continuous data to prevent polling (which kills)
* Github
	* Let clients make use of HTTP caching to help you out
	
## Code Separation
* Github
	* First API was inline with the UI (respond_to) - now use separate controllers because the logic was getting kludgy
	* Eventually want to separate into its own app (Sinatra or like)
* Twitter
	* Nice when you can make all of the business logic in one place
	* If you use skinny controller/fat model, it's not much duplication
	* Much easier to reason through changes, figure out logic if it's separate
* 37S
	* This is why respond_to exists - use it!

## Security Concerns ##
* Twitter - annotations present an interesting challenge as they can store any data
	* Moral:  you're going to get screwed, no matter how awesome you are.  Be prepared.
* Github
	* Watch out for login cookies/sessions, particularly with badges
* 37S - keep the walls up

## Developer Communication ##
* Twitter - obviate the need to communicate with developers
	* Detailed status sites, etc.  Keep them in the loop.
	* Use what works for you
* Thoughtbot - make sure you're not totally wrong
	* People will hold you totally responsible for what you say
* 37S - figure out what business priority you have for the API and treat it like a product with devs as customers
	* Get rid of your API if you're not going to support it
* NYT - build sample apps for documentation for every change.  Helps you learn too, be able to relate.
* Github - foster a sense of community and open lines of communication
	* Community-supported docs, etc.
	* Be your own customer (37S highrise iPhone app)