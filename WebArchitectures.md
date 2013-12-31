# Web Architecture

To build web architectures that scale to millions of users, one has to
distribute the work load to many machines.  In the classical setting
one uses load balancing to spread out the work to many web servers and
a distributed database with many read-mirrors for data access.

## Vertical vs. Horizontal Scaling

Vertical Scaling - buy bigger boxes. Distrubte work to many threads.
Horizontal Scaling - buy more boxes.

VS has a natural upper limit. You can not buy more than 80 Cores and 5 TB Ram.
HS can exploit cheap hardware. Linear cost scaling.

If you want to build very lager applications, or need to watch out for money, you need HS.

## Session Handling

Major Problem: Maintaining state.

Three approaches:

* Local Sessions - App server maintains session
* Centralized Sessions - Handle state in a central data base
* No Sessions - Put everything in the cookie / Let the client maintain state.

## Service Oriented Architecture

Key Ideas:

* Divide the web application into independent services
* Each services maintains its own data
* Web Server has only a proxy function
   * it distributes requests to the services

## Event Driven Web Servers
* http://stackoverflow.com/questions/6089091/why-use-mongrel2

# References
* [B. Erb -  Concurrent Programming for Scalable Web Architecturs (book, thesis)](http://berb.github.io/diploma-thesis/community/index.html)
* [Theo Schlossnagle (OmniTI) - Scalable Internet Architecture](http://www.slideshare.net/postwait/scalable-internet-architecture)
* [James Falkner - Asynchronous Web Programming with HTML5 WebSockets and Java](http://www.slideshare.net/schtool/asynchronous-web-programming-with-html5-websockets-and-java)
* [Carl Henerson - Scalable Web Architectures: Common Patterns and Approaches (slides)](http://www.slideshare.net/iamcal/scalable-web-architectures-common-patterns-and-approaches-web-20-expo-nyc-presentation)
* [Slides on Nodejs and ZMQ (slides)](http://www.slideshare.net/fedario/zero-mq-with-nodejs)

