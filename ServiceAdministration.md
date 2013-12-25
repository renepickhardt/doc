# Service Administration

I have a bunch of services running on a couple of machines and want
easy means to manage these service remotely. What I have in mind
is something similar as tomcat's manager tool, that allows me
to monitor and control different servlets installed.
What I have instead of servlets are:

* python scripts
* java tasks
* webservers

### Whishlist
* web dashboard
* Remote controled starting and stopping of services
* Service Monitorign
* Log information
* System Monitoring: CPU load, Network load

Ideally I would have a method to start jobs and register them for monitoring, e.g.

    register python script.py
    register mvn exec -Dmain=hh.bla.main

Then I could look at my web-dashboard at `localhost/monitor` and see the tasks running
together with statistics about:
* CPU usage
* Memory usage
* Network usage
* disk IO ...
Moreover the output to `stdout` and `stderr` is shown in a text view.

In a distributed environment there would be a central dashboard, where I can monitor
the taks on different instances.

### Monitoring Tools
* [Naigos](http://www.nagios.com/)
  By far the most popular monitoring tool. The basic version is free and open source version,
  but it seems the really usefull stuff (grahics, dashboards) need a license.
* [mmonit](http://mmonit.com/monit/) Graphs only commercial.
* [ganglia](http://ganglia.sourceforge.net/)
* [Reconnoiter](http://labs.omniti.com/labs/reconnoiter)
  Cusom monitoring solution by OmniTI (Theo Schlossnagle). Targets high performance computing.

#### Gangila
Seems to be the most promising tool. Developed at UBerkley. Free software.
Scales to multiple nodes and multiple clusters. Oreilly has a book on it.

It installs right away with `apt-get install ganglia-monitor
ganglia-webfrontend`.  To get the web-frontend running set an alias in
the apache configuration as described in the [mailinglist](https://www.mail-archive.com/ganglia-general@lists.sourceforge.net/msg06092.html):

    sudo cp /etc/ganglia-webfrontend/apache.conf /etc/apache2/sites-enabled/ganglia

Architecture
Ganglia consists of three different services `gmond`, `gmetad` and `gweb`.

* `gmond`  
  lightweight process that monitors system ressources and broadcast them on the local subnet.
  Also it receives broadcast messages from the neihbouring nodes and makes them accessible for polling by `gmetad`.
  It is important to note, that only the current state is rembered by the `gmond` instances and no historical data.
* `gmetad`  
  Service that aggregates status information from multiple
  clusters. Per cluster it is sufficient to poll **one** `gmond`
  instance, since the state is shared among the nodes.
* `gweb`  
   web dashboard for `gmetad` nodes.

Architecture Scetch

         gmond                gmetad      gweb
         =====                ======      ====
      * <-----> * <---[poll]---> * <-------> *
      | Cluster |                |
      * <-----> *                |
                                 |
      * <-----> * <---[poll]-----+
      | Cluster |
      * <-----> *

Configuration of ganglia can be found in  `/etc/ganglia` in two well organized files.

##### gmond

* Gathers data from local system on an independent schedule.
* Implication: System does not rely on external polling. Many
  independent poller can queery the cluster. E.g. `gmond-zeromq`
  publishes data on zmq bus.
* gmond seems to run single threaded: (cf. `ps -eLf | grep gmond`)
* Can be extended to report metrics provided by scripts in any
  language. Especially easy: C, C++, Python. `gmetric` tool provided.
* Metics are shared between gmond nodes via [multicast channels](http://en.wikipedia.org/wiki/IP_multicast)

* Configuration in `/etc/ganglia/gmond.conf`
  - Configure multicast channels
  - Add customized metrics. (modified by `gmetric` tool)



### Log Aggregators
Log Aggregators specialize on the task of getting log information from services running on different hosts storing
them in a central repository (like mongo, hive, hdfs) and providing querry interfaces and analytics.

* [Jason Wilder - Centralized Logging Archtictures](http://jasonwilder.com/blog/2013/07/16/centralized-logging-architecture/)  
  Great blog-post that sums up all the essential information. Jason has also more blog posts on related topics like logging web servers in his blog.
* [fluentd](http://fluentd.org/)
  used at nintendo.com slideshare.net. Seems very easy to deploy.
* [Apache Kafka](http://kafka.apache.org/) developed at facebook.com.
* [Apache Flume](http://flume.apache.org/) 
* [Logstash](http://logstash.net/) built ontop of redis, elasticsearch
* [graphite](http://graphite.wikidot.com/) Log analytics.

### Application Manager
* upstart - init script manager.
* [pm2](https://github.com/Unitech/pm2) - nodejs process manager
* [DiProcD](http://projects.ceondo.com/p/diprocd/) promisses much, seems immature
* [Grunt.js](http://gruntjs.com/) Task Runner. Seems a perfect fit but js-only!
* [job-runner](https://github.com/spilgames/job-runner) python tool for running jobs on remote servers. Uses zmq.


#### Job Runner

Installation is a major headache. This script seems to be maintained
by a single person, or two, which are not active any more.

Installation notes:
* Grappelli has to be downdated: `pip install django-grappelli==2.4.8`
* Gevent-websockets has to be downdated: `pip install gevent-websocket==0.3.6`

The dashboard is django powered and looks reasonably nice.  The
usability however needs improvement. It takes 5 stages to run a first
sample task, including the cration of a project template. Information
about the infrastructure (no. of connected workes) is not visible.

Architecture Notes:
The job-runner worker can run arbitrary python scripts that are sent
to them from a central server. The scripts are executed and the output
is sent back to the server.

Job-Runner uses zmq for internal communication. The web front-end is connected
via a zmq-websocket bridge: gevent-websockets.

