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

It installs right away with `apt-get install ganglia-monitor ganglia-webfrontend`.
To get the web-frontend running read the [mailinglist](https://www.mail-archive.com/ganglia-general@lists.sourceforge.net/msg06092.html).


### Application Manager
* upstart - init script manager.
* [pm2](https://github.com/Unitech/pm2) - nodejs process manager
* [DiProcD](http://projects.ceondo.com/p/diprocd/) promisses much, seems immature
