# Architectures for real-time data

by Theo Schlossnagle,
Source: [YouTube](http://www.youtube.com/watch?v=OkUxOpSAqh0&list=WL0D19AD38E5D9889F)

Main Task: Monitoring

Challenge: Sub second reactions on incoming events (~100ms) -> Alerts, Messages

## Data Storage

* Graphing an visualization
* Statistical Analysis (show std. deviations, mean values, quantile ranges)
* Data Export
* Real-time querying

Real-time challenge: Have to combine incoming data with context from
earlier events.

### Architecture

* Central lookup table: Map metrics to tables
* Tables with metric data are spread out on servers
* 'Map-Reduce' like tasks (meean, std deviations, derivatives) are
  computed locally.

### Storage Technologies

#### SQL Server

* Use SQL Servers gives flexibility to change data when it is already
  ingensted.
* Overhead caused by SQL functionality e.g. ACID.
* Shards are replicated individually (good practice cf. Flicker, Yahoo).
  Implications on storage consumption (!), performance

#### NoSQL

Looked at HDFS, Riak, Cassandra, Mongo DB but failed because of

* Data Storage
* Data Safety
* IOps Problem

HIVE might have made it.

Two people in company that undestand distributed systems very well! 
-> Who is that?

Only do commutative operations (not dependend on order) on data!

### Snowth Database

Special purpose solution for real time anaysis of monitoring data.

* Written in C (as god intended)
* Stores aggregated "rollups", i.e. timed buckets of data, with summary layers.
* Typical query: Give me 700 equally spaced data points of access
  numbers of server X from time A to B. 
* Fixed record lengths: I don't need indices! -> Use file offsets!
* Data storage format: Consistent hashing algorithm (like Dynamo) Ring
  of UUIDs of server. SHA1-Hash function decides which server is
  chosen for data.
* Support for Stored Procedures in LUA (brilliant C embedding)
* Compression (by 97% in example)

### AMQP / RabbitMQ

Transport data from nodes to central analytics compont.  
Use: AMQL/RabbitMQ -> Difficult to use but works.  
Fallback: HTTP!

### Stream Processing Tools


* Old. Used mainly for stock exchange: Truviso, SQL Stream, DROOLS,
  OpenESB, Esper (<- used)
* New: S4 (Yahoo), Storm (Twitter), hop (Hadoop Online Prototype)

Experiences:

* Debugability is very hard! Distributed, Stream processing

# Reconnoiter

source: <http://omniti.com/video/noit-oscon-demo/index.html>

Reconniter is a monitoring system

### Design Goals

* >10K checks on cheap hardware
* Centralized configuration  management
* Decentralized configuration manipulation
* Decoupling of data collection from visualization and anaysis
* Do not throw data away.

### Architecture

<img src="http://labs.omniti.com/labs/reconnoiter/browser/docs/assets/noit-network-arch.png?format=raw" width="100%">

#### Components

* noitd  
  Run checks, various protocols. Extendable in C and LUA. Stores
  locally on disc.
* Stratcond - Strategy Console.  
  Connects to all notids. Stores data in (sharded) PostgreSQL
  server. Servess web console. View on all checks, and trending data.f
  
#### Features

* security via SSL
* data loss prvention by local storag in noitd
* telnet access to noitd similar to cisco console

#### Web Console

* Flexible graphing features using canvas objects
* Combine graphs in dashboards
* Export plots as standalone URIs
* Real time graphing!
