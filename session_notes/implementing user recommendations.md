# Implementing user recommendations in Rails
* Matthew Deiters - @mdieters
* Make graph technologies accessible
* VW Lemon ad - brought on the golden age of advertising *creative revolution
	* Previously had been more of an academic approach
	* Technology changed (TV - by 1962, 90% of homes had one)
	* Necessarily, the approach changed
	* Transition to audience segmentation (away from male-targeted exclusively)
* Web dev is in a similar place now, particularly with NoSQL and dynamic languages
* We've been doing CRUD for 30 years, and it's gotten really boring for us and for users

## Our Creative Revolution
* Recommendations = Money
	* 25% of Amazon's sales are on personalized suggestions
	* StumbleUpon ranks #1/#2 in social media traffic generations
* 3 points
	* Discover the relationships in your data
		* FB news feed - using your actions to determine who's relevant to you
		* Github relationship graph with followers
		* Applies to social networking, content display (tailoring content display), website analytics, and predictive analysis
	* Modeling the relationships
		* RDBMSes don't work for this dataset and operations  b/c SQL is set-based
			* With 100 people, it's really fast
			* Doesn't scale though - 2nd degree ops with 60k people takes over an hour
			* Get some really bad SQL smells - RECURSIVE SQL extension and stuff like that (Hasselhoff graphic)
		* Use graphs instead - relationships are first class citizens in the data model
			* Instead of rows, you have nodes (or actors or vertices or points)
			* Edges are relationships between those nodes (or arcs or links)
			* Ultimately less complex and more natural (and whiteboard friendly)
			* Plus they're really fast (FUCK YOU, EINSTEIN)
	* Using graphs in your Rails app
		* In-Memory Ruby Graphs
			* Great for small datasets or ad-hoc querying
			* There's the RGL gem but it's cryptic (github.com/fmeyer/rgl)
			* 20k nodes and 1M edges = 300MB of memory (not bad) plus queries went from 30 seconds to a few milliseconds (smallish dataset though)
		* Persistence & Dynamic Data - Neo4j (OSS graph DB in Java)
			* Key-value store plus relationships between keys
			* Neo4j.rb is a solid interface library (ORM) using Lucene but requires JRuby
			* Don't need to store everything in Neo4j - just another persistence tool like Solr
			* Neo4jr-social is another library - more like solr/sunspot, much easier to run and doesn't require JRuby
				* Self-contained jetty server or deployable WAR
				* Focused on social network analysis
				* Built-in basic querying, very extensible
			* (Live code example w/ Star Wars stuff) - built in relationships, suggestions, degrees of separation
		* Gremlin - xPath-like syntax for querying your social graph
			* Had a MongoDB backend - cool but not efficient
			* Also works on Neo4j
			* More than anything, it's a query syntax that's really easy to use
			* Net net, SQL is assembly required, Graphs mostly work out of the box with pre-built queries
				* AllSimplePaths - who do we have in common?
				* ShortestPath
				* Dijkstra algorithm - add scores to edges
					* Can build who do we have in common on steroids
				* Closeness Centrality - who has the most followers on Twitter
				* Betweenness Centrality - Who has more influential people following them on Twitter (used for the Github London followers graph)
				* Eigenvector Centrality - Essentially PageRank
			* Performance implications
				* Sparse graphs are faster because there's less traversal necessary
					* if you get too dense the data isn't especially meaningful
				* Neo4j is a hoss - needs lots of RAM to avoid swapping
				* Metadata - can vary depending on how you want to slice and dice and limit
				* Breadth-first vs. depth-first searches - need to match to data and the problem you want to solve

## Conclusion
* Start thinking in graphs when it makes sense
* NoSQL as an augmentation like Solr or Sphinx or Memcached - okay to have different stores for specific kinds of data
	
# Q&A
* Probably don't want to store a lot of metadata in Neo4j - use identifiers and relationships, and node data where necessary
* Performance is limited by complexity of relationships, not size of dataset
* Nodes and relationships can be of any type
* Dex graph engine - C-based, could be easy to bind into Ruby.  Also RichRelevance (SaaS) and DirectedEdge (Shopify)
* For loading data, mine info out of relational DB and put it into Neo4j
	* Not necessarily duplicate data, just modeled for different purposes
* Deployment - Neo4j is a memory hog, may be able to keep the DB in memory but not sure how well it releases RAM