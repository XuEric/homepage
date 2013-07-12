---
layout: master
title: MQ HA with MQ Client Connection Define Table
---
# {{ page.title }} #


## Define Server Connection at Gateway QM ##
在TPQM上定义：
```
#!sh
echo "DEFINE LISTENER (LSR1) TRPTYPE(TCP) CONTROL(QMGR) PORT(31000) REPLACE" | runmqsc TPQM
echo "START LISTENER (LSR1) " | runmqsc TPQM
echo "DEFINE CHANNEL(SVRCONN) CHLTYPE(SVRCONN) TRPTYPE(TCP) DESCR('SVR CONN') REPLACE" | runmqsc TPQM
echo "DEFINE CHANNEL(SVRCONN) CHLTYPE(CLNTCONN) TRPTYPE(TCP) CONNAME('127.0.0.1(31000)') DESCR('clnt conn to TPQM') QMNAME(TPQM) REPLACE" | runmqsc TPQM
```
在TPQMBK上定义：
```
#!sh
echo "DEFINE LISTENER (LSR1) TRPTYPE(TCP) CONTROL(QMGR) PORT(32000) REPLACE" | runmqsc TPQMBK
echo "START LISTENER (LSR1) " | runmqsc TPQMBK
echo "DEFINE CHANNEL(SVRCONNBK) CHLTYPE(SVRCONN) TRPTYPE(TCP) DESCR('SVR CONN') REPLACE" | runmqsc TPQMBK
```

## Define Client Connection Channel at Gateway QM ##
在TPQM上定义：
```
#!sh
echo "DEFINE CHANNEL(SVRCONN) CHLTYPE(CLNTCONN) TRPTYPE(TCP) CONNAME('127.0.0.1(31000)') DESCR('clnt conn to TPQM') QMNAME(TPQM) REPLACE" | runmqsc TPQM
echo "DEFINE CHANNEL(SVRCONNBK) CHLTYPE(CLNTCONN) TRPTYPE(TCP) CONNAME('127.0.0.1(32000)') DESCR('clnt conn to TPQMBK') QMNAME(TPQM) REPLACE" | runmqsc TPQM
```

```
#!java
package mqclient;

import java.net.URL;
import java.util.Hashtable;

import com.ibm.mq.*; //Include the WebSphere MQ classes for Java package

public class MQSample {

	// define the name of the QueueManager
	private static final String qManager = "*TPQM";

	// and define the name of the Queue
	private static final String qName = "SYSTEM.DEFAULT.LOCAL.QUEUE";

	// main method: simply call the runSample() method
	public static void main(String args[]) {
		new MQSample().runSample();
	}

	public void runSample() {

		try {
			MQEnvironment.enableTracing(1); // start trace

			java.net.URL chanTab1 = new URL(
					"ftp://esbsd:esbsd@52.97.0.11/../../var/mqm/qmgrs/TPQM/@ipcc/AMQCLCHL.TAB");

			MQQueueManager qMgr = new MQQueueManager(qManager, chanTab1);

			// Set up the options on the queue we wish to open
			int openOptions = MQC.MQOO_INPUT_AS_Q_DEF | MQC.MQOO_OUTPUT;

			// Now specify the queue that we wish to open and the open options
			System.out.println("Accessing queue: " + qName);
			MQQueue queue = qMgr.accessQueue(qName, openOptions);

			// Define a simple WebSphere MQ Message ...
			MQMessage msg = new MQMessage();
			
			// ... and write some text in UTF8 format
			msg.writeBytes("Hello, World!");

			// Specify the default put message options
			MQPutMessageOptions pmo = new MQPutMessageOptions();

			// Put the message to the queue
			System.out.println("Sending a message...");
			queue.put(msg, pmo);

			// Now get the message back again. First define a WebSphere MQ
			// message to receive the data
			MQMessage rcvMessage = new MQMessage();

			// Specify default get message options
			MQGetMessageOptions gmo = new MQGetMessageOptions();
			gmo.waitInterval = 5000;

			// Get the message off the queue.
			System.out.println("...and getting the message back again");
			queue.get(rcvMessage, gmo);

			// And display the message text...
			System.out.println("The message is: " + rcvMessage.getDataLength());

			System.out.println("please check the network connection....");

			// Close the queue
			System.out.println("Closing the queue");
			queue.close();

			Thread.sleep(100000);

			// Disconnect from the QueueManager
			System.out.println("Disconnecting from the Queue Manager");

			qMgr.disconnect();
			System.out.println("Done!");
		} catch (MQException ex) {
			System.out
					.println("A WebSphere MQ Error occured : Completion Code "
							+ ex.completionCode + " Reason Code "
							+ ex.reasonCode);
			ex.printStackTrace();
		} catch (java.io.IOException ex) {
			System.out
					.println("An IOException occured whilst writing to the message buffer: "
							+ ex);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
}
```