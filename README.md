# IoT Control 
IoT Device Control Application

To view a demo of this applicaton you can go to http://robertburich.com, there is a link to the demo from the Projects page on my website. 

This project allows me to utilize a web application to change the state of an IOT device.  I wanted to test out the Drag-n-Drop capability of HTML5 so I use that capability built into the browser.  You can basically change the color of the IOT device by dragging and dropping one of the color blocks on top of the IOT device.  You can also turn the IOT device on/off by clicking on the on/off button.

The IoT device is an older Intel Galileo Gen2 board running a version of Yocto Linux. The device is controlling an LED that can be turned on/off and the colors can be changed to red, blue or green by dragging and dropping the colored box of choice onto the box labeled IoT Device. This is all accomplished via the web interface on my website. Following are some of the high level technical details of how this is accomplished:

The Web application and server (running in the AWS cloud) uses Linux, Apache, MySQL, PHP, Javascript, jQuery – AJAX, and HTML5/CSS3 Drag-n-Drop features to allow the client to make changes to the IoT device.

I chose to use the MQTT protocol to communicate to the device (through the AWS cloud) as opposed to MQTT over Websockets protocol. I’ll probably go back and retry it with MQTT over Websockets later.

A change in state (i.e. color change, on/off) of the IoT device is accomplished by communicating with the IoT Shadow device in the AWS cloud through the Web Application. All changes in the IoT device state are requested (via the Web application) and reported (from the IoT device) using the IoT Shadow device in the AWS cloud. This MQTT protocol is used for this communication.

The IoT device also communicates with an AWS IoT topic (in the AWS cloud representing the LED device). The AWS IoT topic uses a “RULE” such that whenever the IoT topic is updated, an update (new row) is added to a DynamoDB table with the current IoT device status (i.e. the color and on/off state). So, every time the IoT device changes its state, a new row is added to the DynamoDB table. An update of the DynamoDB table also triggers a Lambda function to add a timestamp to the new row in the DynamoDB table.

The Intel Galileo Gen2 board is running Python which actually performs the Device control – turning the LED on/off and changing color. Python is also used to interface to the AWS IoT Topic (IoT device) in the cloud along with what AWS refers to as the Shadow device (also in the AWS cloud). Again, this communication utilizes the MQTT protocol.

The Trace History Button selects all of the current entries for the IOT device from the DynamoDB table and displays them in the IoT Device History Table. This is accomplished by using Amazon's API gateway and a Lambda funciton.  Currently, if you request changes to the IoT device state you must refresh the table to see the new entries.

Whenever the IoT Device changes state, it also takes a snapshot of the device and stores that image in AWS S3. If you select any of the timestamps in the Timestamp column of the IoT Device History Table, the snapshot (image) that corresponds to that timestamp is displayed on the IoT Device Control Page. 
