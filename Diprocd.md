# DIPROCD - DIstributed PROcess Control Daemon

In this note we are going to install
[diprocd](http://projects.ceondo.com/p/diprocd/) on a virtual linux
cluster.

License: ?

Popularity: The tool seems to be rather smallish without a large
adaptation. It is mentioned in the document
[A Private PaaS with Mongrel2 and ZeroMQ](http://notes.ceondo.com/mongrel2-zmq-paas/) -
which got me interested in playing around with it.

The [documentation](http://projects.ceondo.com/p/diprocd/) consists of
a single page. This note can be seen as a slight extension of the
official docs.

## Architecture

dirpocd is a small-ish collection of python scripts (total of ~5K
lines). It consists of three components

* `dpd-masterd` central process manager, that publishes the new of processes for each node
* `dpd-clientd` subscribes to the updates from dpd-masterd and update the configuration file of dpd-workerd
* `dpd-workerd` listens to changes of the configuration file and executes processes on the local macine accordingly.

those components are connected via the [zmq](zeromq.org) networking library.

### Architecture Sketch

     HOST                      SLAVES
     ----                      ------
	 
     * [dpd-masterd|PUB]--+--> * [SUB|dpd-clientd] --> (config file) <-- [dpd-workerd]
                          |
                          +--> * [SUB|dpd-clientd] --> (config file) <-- [dpd-workerd]
                          |
                          +--> * [SUB|dpd-clientd] --> (config file) <-- [dpd-workerd]

Legend:
- `*` stand for individual machines.
- `[...]` stand for running processes.
- `|PUB]` stands for a zmq PUB socket


# Installation

Installation is explained
[here](http://projects.ceondo.com/p/diprocd/) and worked fine for me
on the host system:

    git clone git://projects.ceondo.com/diprocd.git
    cd diprocd
    sudo python setup.py install

Make sure you have `pyzmq` and `simplejson` modules installed by running:

    sudo apt-get install python-setuptools python-dev
    sudo easy_install pyzmq
    sudo easy_install simplejson

# Running diprocd-worker

We use the dpd-worker to run a first simple sleep task for us.
First we create an appropriate configuration file:

    cat << EOF | tee dpd-sleep-worker.json
    {"pid_file": "/tmp/dpd-workerd-x.pid",
     "log_file": "/tmp/dpd-workerd-x.log",
     "procs": [{
	    "name": "sleep-worker-1",
	    "run": "/bin/bash",
		"args": ["-c","echo 'hello from dpd-worker!' && sleep 10"],
	    "user": "nobody",
	    "pid_file": "/tmp/sleep.pid", 
	    "chroot": "/",
	    "env": {},
	    "cwd": ".", 
	    "restart": true
		}]
	}
	EOF

Remarks:

* In the first two lines we specify the pid & log files for the worker-daemon itself.
* The `procs` array contains dicts for every process that shall be executed.
* Inside the process dict, we specify the execution environment:
  executable, command line arguments, user, base dir

Now start the worker using

    sudo dpd-workerd -f -v dpd-sleep-worker.json

The su-rights are needed, since `dod-wordkerd` spawns processes as
different users. The `-f` command line switch tells dpd-workerd to run
in the foreground. You should see a lot of debug messages telling you
that sleep-worker-1 is being executed. Every second another line of

    DEBUG:root:Supervise sleep-worker-1.

appears on the screen, telling you that `dpd-workrd` waits for the
process to end. After 10 seconds the debug messages are repeated as
the process respawns.

If you edit the config file while running the worker. It will notice
the changes, as expected, and try to re-run the script. However, it
will fail most of the time with a `diprocd.errors.OpExecError - 'File
already locked'` error.

## Running dpd-masterd

Now we want to distribute tasks from a central authority to the worker
tasks. For the beinging we stay on a single machine. To do so we
create a config file:

    IP=127.0.0.1 # put your IP here
    cat << EOF | tee dpd-master.json
    {
        "pid_file": "/tmp/dpd-masterd.pid", 
        "log_file": "/tmp/dpd-masterd.log", 
        "master_stats": "tcp://$IP:31123", 
        "master_updates": "tcp://$IP:31124",
        "nodes": {
    	"N1": [{
    	    "name": "sleep-worker-1",
    	    "run": "/bin/bash",
    	    "args": ["-c", "echo hello from dpd-master && sleep 5"],
    	    "user": "nobody",
    	    "pid_file": "/tmp/sleep.pid", 
    	    "chroot": "/",
    	    "env": {},
    	    "cwd": ".", 
    	    "restart": true
    		}]
        }
    }
	EOF

Start the master node using

    dpd-masterd -f -v dpd-master.json

The master daemon does not require any special rights and can eveen be
run as user `nobody`.

You should see the following output:

    INFO:root:Collect stats on tcp://127.0.0.1:31123.
    INFO:root:Publish updates on tcp://127.0.0.1:31124.
    INFO:root:Sleep 2 seconds to let clients connect.
    INFO:root:Publish to node node1 1 processes.

The third line most likely referrers to the infamous
["slow-joiner" syndrom](http://zguide.zeromq.org/page:all#Getting-the-Message-Out).
When you edit the `dpd-master.json` file the server automatically
distributes the updated config.

To see what actually happens we can subscribe to the zmq-PUB socket
and listen to the messages. I have written a small script that
simplifies this process.

     wget https://raw.github.com/HeinrichHartmann/tools/master/zmqdump
	 chmod +x zmqdump
	 ./zmqdump SUB tcp://127.0.0.1:31124

If you now restart the daemon, you will see the following message:

     N1 [{"chroot": "/", <...> : "restart": true}]

Note that this is just the `node_name` followed by a whitespace and
the json configuration.

## Running dpd-clientd

After we have the master up and running, we want to bring up a client
that receives the run configurations. To do so we, again create 
configuration files.

    IP=127.0.0.1 # IP of the master node
    cat << EOF | tee dpd-clientd.json
	{
	    "pid_file": "/tmp/dpd-clientd.pid",
        "log_file": "/tmp/dpd-clientd.log",
        "master_stats": "tcp://$IP:31123",
        "master_updates": "tcp://$IP:31124",
        "node_name": "N1",
        "conf_file": "/tmp/dpd-worker.json"
    }
	EOF

    # Initialize /tmp/dpd-workerd.json
	echo "{}" > /tmp/dpd-workerd.json

It is crucial to get the zmq-socket addresses `master_updates` and
`master_stats` right. They have to match with the ones provided in the
master configuration. Also `node_name` has to match with the master
config.  In the official documentation, the `node_name` is set to
"%h", which resolves to the hostname.

### Testing

Now start the client script by running

    dpd-clientd -f -v dpd-clientd.json

If you now restart the master, to resend the messages, you should see
the following output

    DEBUG:root:N1 [{"chroot": "/", <...> "restart": true}]
    INFO:root:Got 1 processes in update.

and `cat /tmp/dpd-workerd.json` should reveal the json config of N1
speficied in the master config.  You can have some more fun by
monitoring the dpd-worker.json file

    watch -n1 "cat /tmp/dpd-workerd.json | python -m json.tool"

and editing the master config. You should see the updates propagating
to the worker configurations.

WARNING: `dpd-workerd` will not run with this config.

### Upshot

The whole effect of the `dpd-masterd` and `dpd-clientd` scripts is to
distribute parts of the `dpd-master.json` to the worker nodes and save
them in the `procs` section of `dpd-workerd.json` file:


     * HOST:dpd-master.json
       {
	     'N1': [payload1],  -->  * N1:dpd-worker.json { procs: [payload1] }
	     'N2': [payload2],  -->  * N2:dpd-worker.json { procs: [payload2] }
		 ...
	   ]

This is actually a very generic task, that is also addressed by rsync
or dropbox.

## Putting it together

Now that you have `dpd-master` and `dpd-client` it remains to start
`dpd-worker` again. If we run

    sudo dpd-workerd -f -v /tmp/dpd-workerd.json

However, this will fail with `KeyError: 'pid_file'`. The reason is,
the `dpd-workerd.json` does not receive the `pid_file` and `log_file`
fileds from the master. We have to add them manually:

	cat << EOF | tee /tmp/dpd-workerd.json
	{
	    "pid_file": "/tmp/dpd-workerd.pid",
		"log_file": "/tmp/dpd-workerd.log",
		"procs":    []
	}
	EOF

Now start the daemon again and everything should be fine.
