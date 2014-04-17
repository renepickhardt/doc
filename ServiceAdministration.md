# Remote Execution Framework

A distributed architecture consists of several independent services
running on a different machines. These services need to be up and
running all the time and communicate with each other using message passing.
In order to maintain such an architecture it is critial to supervise
the machines and services closely.

What I have in mind is something similar as tomcat's manager tool,
that allows me to monitor and control different servlets installed.

### Whishlist
* Service Monitorig
* Gathering of log information
* web dashboard
* Remote controled starting and stopping of services
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

In a distributed environment there would be a central dashboard, where
I can monitor the taks on different instances.

I was unable to find such an execution fram

# Existing Tools


### Monitoring Tools
* [Naigos](http://www.nagios.com/)
  By far the most popular monitoring tool. The basic version is free and open source version,
  but it seems the really usefull stuff (grahics, dashboards) need a license.
* [mmonit](http://mmonit.com/monit/) Graphs only commercial.
* [ganglia](http://ganglia.sourceforge.net/)
* [Reconnoiter](http://labs.omniti.com/labs/reconnoiter)
  Cusom monitoring solution by OmniTI (Theo Schlossnagle). Targets high performance computing.

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
* [upstart](http://upstart.ubuntu.com/) event-based replacement for the /sbin/init
* [daemontools](http://cr.yp.to/daemontools.html) - a collection of tools for managing UNIX services.
* [runit](http://smarden.org/runit/) - a UNIX init scheme with service supervision
* [supervisord](http://supervisord.org/)  a client/server system that allows its users to monitor and control a number of processes on UNIX-like operating systems.

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

# Ressources

* <http://agiletesting.blogspot.de/2012/09/what-i-want-in-monitoring-tool.html>
* <http://blog.vuksan.com/2012/09/01/my-monitoring-setup/>
* <https://github.com/monitoringsucks>
* <http://www.infoworld.com/d/data-center/puppet-or-chef-the-configuration-management-dilemma-215279>
