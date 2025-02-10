## Putting it all together

Let's now configure a Zeek input and Splunk HEC output and see if we can get raw traffic processed into Zeek logs and then appear in the Splunk **Zeek App for Hunting** app.

All the operations here are done via the UI. You can also do it completely via API. Here are the steps:

1. Create an instance of a Zeek input

![CTB Manager Add Zeek Input](images/4.1-new-zeek-input.jpeg)

Here are the field inputs:
* `name`: anything
* `interface`: en160

The `ens160` is the CTB Broker node admin interface and will monitor all traffic to/from the Broker node.

2. Create an instance of a Splunk HEC output

![CTB Manager Add HEC Output](images/4.2-new-HEC-output.jpeg)

Here are the field inputs:
* `Destination Name`: anything
* `Splunk HEC Token`: <splunk-token>
* `HEC Endpoint URL`: http://<managerIP>:8088/services/collector/event

3. Connect the Zeek input to the Splunk HEC output

Simply drag and connect the input to the output.

Wait a few minutes and you will see the input and output turn green and data should start flowing

4. Visualize the data in Splunk

Login to the Splunk dashboard on your CTB Manager:
`http://<manager-IP>:8000/`
with username and password (same as for CTB Manager)

Click on the **Searching and Reporting** App on the top left and select `index=main` in the search bar to see if Zeek data is being populated.

If Zeek data is being populated, click on the **Zeek App for Hunting** app on the left and `Sensor Health` tab to see the data flow.

## Closing thoughts

Each of your Workshop setup will be available until the end of the week. If you need more time, you can reach out to me in the DEVWKS-2478 webex room or directly via my email address (ajthyaga@cisco.com).

Feel free to experiment with the existing plugins. You can do so as follows:
- Remove the input and output via the UI
- Then login to the Broker Nodes over ssh and for each plugin, you can run the `uninstall` scripts which are present in the same directory as the install scripts.
- You can adjust the input JSON parameters to add more fields for example.

And finally, all the UI operations are available via API. The API is exposed on the CTB Manager at https://<manager-IP>/api-v1/. You can create the input, destination and connect them via API as well.
