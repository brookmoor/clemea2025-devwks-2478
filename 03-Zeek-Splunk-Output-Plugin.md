## Splunk HEC Output Plugin

The Splunk HEC output plugin has already been installed on the Manager.

Let's install it on the Broker Node.

```
admin@tb-213-master:~$ cd hec/install-213/
admin@tb-213-master:~/hec/install-213$ ls
check_hec_output_type  docker-compose.yml.patch  h.d  install_hec_plugin.sh  telegraf  uninstall_hec_plugin.sh
admin@tb-213-master:~/hec/install-213$ 
```

You can examine the file `install_hec_plugin.sh`. The install script creates a `hec` plugin directory for all hec related files, followed by some cron jobs that monitor for creation/deletion of a Splunk HEC destination and statistics reporting. In the case of HEC, we are using a lot of the functionality that is present in `telegraf` like was mentioned before. You can examine the source code here to get a better understanding. The file `h.d/start_hec.sh` has details about the telegraf plugins and configuration.

Now, we can just go ahead and run the install script which will do all of the above.
```
admin@tb-213-master:~/hec/install-213$ sudo ./install_hec_plugin.sh 
Installing the Splunk HEC Output Plugin
Manager IP: <manager-IP>
Manager web username: admin
Manager web password: 
checking if HEC output exists
HEC output type configured
Creating HEC output plugin on Broker Node
patching file /opt/titan/compose/docker-compose.yml
You should now be able to create a new HEC output
```

## [Zeek to Splunk](04-Zeek-Splunk-Telemetry.md)