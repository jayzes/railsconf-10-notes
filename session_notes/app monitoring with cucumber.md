# App Monitoring with Cucumber
## Evolution of testing
* Save & Refresh -> Test::Unit (difficult to demonstrate business value) -> RSpec + BDD (more stakeholder-digestible) -> Cucumber (awesome)
* Cucumber as a business-readable domain specific language
	- Documentation, automated tests, and development aid all in one
* But what about the actual production app?
	* Monitoring is treated as revenue preservation
	* Toolsets not as advanced
	* Only about 12 people in the room have external monitoring for more than a fitter_happier type URL
	* 2 axes - what are you looking at (URLs, etc) and how close are you looking (expected values, etc)?
		* If you're checking just the home page, you have great focus but not breadth
		* 	Ex. search - frequent issues on many sites, may be difficult to detect automatically (0 results for beer!)
	* Not really TATFT on production - why not test where the revenue is being made?
	
## Existing tools for monitoring are old & broken
* Nagios is ugly and tends to have a lot of noise - leads to boy crying work scenarios
	* Nagios is EVIL
* Pingdom - not much depth in tests
* Watchmouse is pretty cool - can drill into specific URLs and methods
	* Twitter is using for enhanced status monitoring
* All tools miss a direct link between business value and the actual alerts being sent out

## Enter cucumber - #devops!
* Blur the line between devs and ops staff, and between development and infrastructure management
* Kumbaya.

## Examples (checkout cucumber-nagios)
* Benchmarking
* E-mail delivery (use mailinator for stubbed e-mail boxes)
* Tap into existing tools like scout and newrelic (cucumber-scout and cucumber-newrelic)
* SEO checks (go to google, check search results)
* Security - check to make sure people have access and others do not (fired employees, etc.)
* Infrastructure status (RAID arrays, etc)
* DNS - make sure everything resolves properly
* Credit card transactions, SSL (valid certificate), error rate (Hoptoad/Exceptional)

## Running features in production
* Use a JSON cucumber output formatter (cucumber-json)
* Set up a separate 'production' environment and feature files/step defs/support
* cucumber-p production
* OR build a cucumber Scout plugins
* Use pagerduty.com to send e-mail alerts out and assigning them out
* Ultimately, this gives you the power to monitor anything and everything with clearly defined business value in a way stakeholders can read and understand, and know of issues before your customers and clients do

## Q&A
* Use hudson for production checks - maintain history, know if things are changing
* Use long-running transactions for dropping generated data.