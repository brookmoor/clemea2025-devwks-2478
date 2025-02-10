# Workshop Exercises

## Initial Setup

Before we get started, every workstation has the following information:

* CTB Manager IP (on AWS)
* CTB Manager login credentials (username/password)

Let us verify a few things first

1. Ensure that you can login to your CTB Manager using the credentials provided using a Browser. If you successfully login, you should see the following:

![CTB Manager Login](images/2.1-ctb-manager-login.jpeg)

2. Go to the `Data Flow` tab and ensure that there are no telemetry flows configured. 

![CTB Manager Dataflow](images/2.2-ctb-manager-dataflow.jpeg)

3. Click `Add Input` and ensure that under `Select Input type`, there is no `Zeek input` type (typically the last option).

![CTB Manager Add Input](images/2.3-ctb-manager-no-zeek.jpeg)

4. Click `Add Destination` and ensure that under the `Select Destination type`, you do see `HEC Destination` (typically the last option).

![CTB Manager Add Destination](images/2.4-ctb-manager-hec.jpeg)

5. We are going to run all of our install commands on the Telemetry Broker Node. To get to the Broker Node, we have to ssh to the CTB Manager on AWS first and then ssh to the Broker node. So, open a Terminal window on your workstation and do the following (make a note of your Workstation's Manager IP). In the example below, the Broker node hostname is `tb-213-master` - each workstation will have a different hostname with the workstation id as the suffix (e.g., tbn-213-5 for workstation 5)

```
devnet@devnet-events: ~$ cd Desktop/DEVWKS-2478
devnet@devnet-events: ~/Desktop/DEVWKS-2478$ ssh -i ./ssh/cleu25-ctb.pem admin@<managerIP>
Linux ip-xxx-xx-x-xx 6.1.0-29-cloud-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.123-1 (2025-01-02) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Feb  5 14:25:55 2025 from 108.18.223.248
admin@ip-xxx-xx-x-xx:~$
admin@ip-xxx-xx-x-xx:~$ ssh -p 2222 admin@localhost
Linux tb-213-master 5.10.0-28-amd64 #1 SMP Debian 5.10.209-2 (2024-01-31) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Feb  5 14:01:04 2025 from 10.0.54.1
admin@tb-213-master:~$ ls
hec  zeek
admin@tb-213-master:~$ 
```

There are two directories on each Broker node which will allow us to install the Zeek and Splunk HEC plugins

## Zeek Input Plugin

Let us now install the Zeek input plugin
```
admin@tb-213-master:~$ cd zeek/install-213/
admin@tb-213-master:~/zeek/install-213$ ls
check_zeek_input_type  install_zeek_plugin.sh    z.d        zeek.tar
create_zeek_plugin     uninstall_zeek_plugin.sh  zeek.json
```

You can examine the file `install_zeek_plugin.sh` and it does the following:

Connects to the Manager and creates a new input type called `Zeek Input`. 

The `create_zeek_plugin` script essentially calls the `https://<manager>/api-v1/input-types/` API on the Manager node and the file `zeek.json` contains the specification for the Zeek input plugin
```
admin@tb-213-master:~/zeek/install-213$ cat zeek.json 
{
    "name": "zeek",
    "config": {
        "fields": [
            {
                "name": "name",
                "type": "string",
                "label": "Name",
                "default": "",
                "required": true,
                "read_only": false,
                "field_type": "text",
                "is_visible": true,
                "max_length": 255,
                "min_length": 1,
                "description": "Zeek Input name",
                "placeholder": null
            },
	    {
		"name": "interface",
                "type": "string",
                "label": "Monitoring interface",
                "default": "",
                "required": true,
                "max_length": 255,
                "min_length": 1,
                "read_only": false,
                "field_type": "text",
                "is_visible": true,
                "description": "Interface for monitoring traffic",
                "placeholder": null
	    }
        ]
    },
    "is_enabled": true,
    "label": "Zeek input",
    "ha_compatible": false,
    "multi_node": false
}
```
As you can see, there are just two key fields that used to configure the Zeek input: `name` and `interface`.

Next, the install script creates a `zeek` plugin directory for all zeek related files, followed by some cron jobs that monitor for creation/deletion of Zeek and statistics reporting. And finally, the Zeek application itself is loaded via a container.

So, we can just go ahead and run the install script which will do all of the above.
```
admin@tb-213-master:~/zeek/install-213$ sudo ./install_zeek_plugin.sh 
[sudo] password for admin: 
Installing the Zeek Input Plugin
Manager IP: <manager-IP>
Manager web username: admin
Manager web password: 
checking if Zeek input exists
Created the Zeek Input Plugin on Manager
Creating Zeek plugin on Broker Node
no crontab for root
5d4427064ecc: Loading layer [==================================================>]  77.88MB/77.88MB
6dac58e89ea4: Loading layer [==================================================>]  3.584kB/3.584kB
93183b3ab20b: Loading layer [==================================================>]  3.584kB/3.584kB
6cdb33e48b23: Loading layer [==================================================>]  3.584kB/3.584kB
9daf1e5c4f06: Loading layer [==================================================>]  235.5MB/235.5MB
e239a62d24eb: Loading layer [==================================================>]  238.6MB/238.6MB
Loaded image: zeek/zeek:latest
Zeek plugin ready on Broker Node

You should now be able to create a new Zeek input
```

6. Go back to your browser window and confirm that the new Input is now available as an option.

![CTB Manager Add Zeek Input](images/2.6-ctb-manager-new-zeek.jpeg)

## [Splunk HEC Output Plugin](03-Zeek-Splunk-HEC-Output-Plugin.md)