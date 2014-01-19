# ZMQ Remote Worker

I am confronted with the following situation: 

* A scraper process is downloading files from a remote location. The
  files are 1-10MB in size, and there are many of them (~500K).
  
* Each file shall pre processed by a worker process, which extracts
  some information (about 1K each) and passes them on to a collector
  process, which does further processing.

What is the right way to design an architecture meeting the following requirements:

* Allow multiple workers. The worker task is CPU intensive and I want
  to be able to exhaust all CPU cores or multiple machines on a cluster.

* Allow multiple scrapers. Further files may be downloaded from
  different sources or on different machines.

![Overview](img/zrw_overview.png)

## All in one

<style type="text/css"> img[alt=Overview] { width: 100%; } </style>
