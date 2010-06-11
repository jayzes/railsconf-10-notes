# User Behavior Tracking
## Three steps to implement successfully
* Track!
* Interpret and identify
* Alternate and refactor

## The toolbox
* Google analytics
* You with Garb
* Vanity

## Questions to answer:
* Which links are being clicked?
* Where are users landing?
* Where are users exiting? frustrations, bounces, etc.
* Where do users spend the most time? most used features, etc.
* What are logged-in users doing? (segmenting users, figuring out what segments are doing, features being used, etc.)
	
## Google Analytics - answers "Which links are being clicked?"
* Async tracking
	* _gaq.push - push tracking values onto an array without actually loading the google JS, then sends them off later
* What should we track? Everything!  More data is always better
* Track clicks as virtual pageviews
	* Assign clicks fake URLs to track appropriately
	* Need to come up with a consistent naming scheme
	* Also works well for modals, etc.
	* May be faulty sometimes - affects time on page measurements, for example
* Can also track events - simple key/value pairs as a counter
	* Main disadvantage is the lack/loss of context
	* Tough to get access to from the API - just a sum count of every event which isn't very useful
* Limitations
	* Time on page measurements can have weird side effects
* An idea - go through the Rails app instead for some events/pageviews
	* Use a meta tag like the Rails 3 CSREF meta tag
	* Insert javascript that picks up the tracking info on load and dumps it out on load
* Can also use something like Clicktale to step through the site

## Garb - answers "Where are users landing?"
* Log in with OAuth access token, set globally or per session in Garb
* Pull back a profile with a UA number
* Reports - can grab existing ones from classes or build new ones the fly
	* Uses a model DSL like DataMapper (Garb::Resource)
	* Specify metrics and dimensions
	* Can specify profile, offsets, limits, dates at query time
	* Returns an OpenStruct with a lot of data
	* Can set sorts and filters as well, with operations and dimensions
	* OR you can build on the fly
	
## Garb - answers "Where are users exiting?"
* Google doesn't give you the exist rate via the API anymore
* Can calculate from exits and page views metrics (just divide!)
* Bummer that you can't sort on the Google side anymore
* What about abandonment?
	* Tracking goals and progress through funnels (you get 4 groups of 5)
	* Can track with a virtual pageview
	* Can't track goals vis a vis events - bummer
	* Can't track from the backend, which kind of sucks too
	
## Garb - answers "Where do users spend the most time?"
* Google doesn't give it to you, but it's just time/(pageviews - exits)
	* Doesn't do this because exit pages always sign out after 30 minutes, so it would just skew results
	
## GA/Garb - answers "What are logged-in users doing?"
* Use custom variables to find out!
	 * SetCustomVar - path, slot # (you get 5), scope, and key-value pair
		* Lasts for the session, might be used to segment admin users
* Great for dynamic segmentation - just grab the right criteria, then can use the ID in reports (grab from the URL, unfortunately)

## Vanity - answers "How do we improve?"
* Redis-backed library for tracking metrics and A/B testing
* Define metrics to be tracked from controller
* Get fancy dashboard of what's going on
	* Would be cool to build programmatic access to the dashboard data - probably pretty easy
* Define ab tests and the metrics they track against
	* takes care of session cookies and segmenting users randomly
	* supports multivariate alternatives
	* Figures out the test stats for you on the fly
	* view helpers for switching display
* Can integrate with Garb too!
	* Give a UA number and the metrics you want to track
	* Can also hit a report instance directly from the metric definition
	* Can get crazy custom if you want
	
## Impacts
* Give info to your users - more transparency and utility - ex. Flickr stats
* Easy admin panel for your users where you can't give them GA access - like Github's pageview stats

## For the road
* Follow the money - for improvements, start at the place where you make your money
* Nobody is paid by the pageview - dig into useful metrics that actually tell you stuff
* User testing still wins - always much easier to get feedback and more direct than trying to infer info from analytics and guessing