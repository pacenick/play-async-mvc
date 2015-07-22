
#play-async-mvc
================

Async framework to disconnect the HTTP client from waiting for server-side futures to complete.


Browser requests to web applications will block, waiting for the request to be processed before the HTML content is returned. If the web application needs to make calls to
back-end systems, the response time cannot be guaranteed!

If the user refreshes the page or returns to a previous page and re-submits the request, this can result in wasted duplicate requests being processed by the application server!
The play-async-mvc library will shield the application servers from processing duplicate requests, and present a more welcoming UI experience to users that are waiting for a request to be processed.

Controllers (play actions) which execute calls on back-end systems will execute a Future, and then block waiting for the Future to complete before returning a response to the client. When a complex Future orchestrates a number of HTTP calls to backend services, the overall transaction time for the Future is variable. The time is variable due to the following factors…

* No agreed SLA with backend HTTP services.
* Socket read timeouts configured to a high value.

If a Future orchestrates to only 2 backend HTTP services during a single user request, the maximum response time for this journey could be 59 seconds due to the large read timeout value.


When systems start to degrade, response times to clients increase. The normal reaction for a client where the page is blocking would be to either refresh the page or press the browser back button and re-attempt to submit the request.
Since the client browser makes a blocked HTTP request to the server side action to service a single page request, any termination of the request will leave the original request running on the application server, which results in wasted transaction/resources being consumed for wasted requests, which in turn can put unnessassary load on back-end systems.


The play_async_mvc library addresses the above concerns using the following features…

* Allow long running Futures to be placed onto a background queue for off-line processing and disconnect the HTTP client from waiting for the Result. This removes the need for the client to remain connected to the server waiting for the result.
* Present auto-refreshing content informing the user the request is being processed, when the request takes longer than 3 seconds to complete.
* Remove long open socket connections between client and server.
* The library supports two modes for running a Future on a background queue. The blocking mode will block the client request a number of seconds when the background Future is initially created, in order to remove the need for the polling page to be presented to the user. The non-blocking mode will always return the polling page.
* Ability to throttle the number of concurrent requests that are currently running on a single application server instance.
* If a number of pages are presented to the user to collect data before a submission is made to back-end systems, the actions associated with the collection of data can be wrapped with a function which checks if a background task is running, and automatically routes the client back to the polling page, or invoke the wrapped action.
* Library is wrapped into a single Trait where the client integrates by extending their play controller action with the Trait.


##Please see the following example controllers…

controllers.ExampleAsyncController	- Example async controller where the client is disconnected from the Future. The example is based on the ExampleNormalController controller where the Future and Result have been separated.
controllers.ExampleNormalController - An example of a normal non-async play action.
