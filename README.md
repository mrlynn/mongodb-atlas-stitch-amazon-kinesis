
# MongoDB Atlas, Stitch and Amazon Kinesis
## Integrating MongoDB and Amazon Kinesis

### Stream Processing
Every day, millions of people visit websites and interact with applications all across the Internet. Their virtual footprints tell a story that can be incredibly valuable. As users interact with the content clicking links, viewing products and maybe even purchasing a product, the stream of click data is compiled and sent to a log file, stored in a database or possibly added to a data stream. This is just one example of streaming data. There are many other examples, across virtually every industry. 

Stream processing differs from batch processing in the speed with which you’re able to derive insight, and therefore value from the data. With batch-oriented data collection and reporting, processing is performed on a periodic basis typically with gaps between execution cycles. With stream processing, streaming data is analyzed in real time using the concept of windows into the data. 

More and more businesses are leveraging modern tools to analyze streaming data in real time rather than having to wait for the data to settle into batches. This is because, over time, the insights become stale and lose value. Consider the example of streaming transactions in a credit card processing use case. The value of analyzing the data and detecting anomalies is extremely high within seconds and minutes of the event whereas, in several days, there’s likely virtually zero value in that specific data.

In this article, I’ll discuss some possible use cases for stream processing.  We’ll look at some tools commonly used to help derive value from the data, and a potential architecture that combines the best features of streaming data management in addition to storing the data durably without incurring the operational overhead associated with deploying and managing an on-premises database solution.

![Value of Data Over Time](https://i.imgur.com/K21Vrl1.png "Figure 1. Value of Data Over Time")
*Figure 1. Value of Data Over Time*

In this article, I’ll discuss some possible use cases for stream processing, some tools commonly used to help derive value from the data and a potential architecture that combines the best features of streaming data management in addition to storing the data durably without incurring the operational overhead associated with deploying and managing an on-premise database solution.

### Amazon Kinesis
Streaming solutions such as [Amazon Kinesis](https://aws.amazon.com/kinesis/data-streams/getting-started/) enable you to minimize the time and cost associated with capturing and taking action on high volumes of incoming data.

Amazon Kinesis is a fully managed service for real-time processing of streaming data at massive scale. With Amazon Kinesis, you can build real-time dashboards, capture exceptions and generate alerts, drive recommendations, and make other real-time business or operational decisions.


There are four primary products in the Kinesis family:
  * *[Kinesis Data Streams:](https://aws.amazon.com/kinesis/data-streams/)* Reliably capture Click-stream, IoT, or other data at high volume and scale.
  * *[Kinesis Data Analytics:](https://aws.amazon.com/kinesis/data-analytics/)* Process and analyze streaming data using standard SQL.
  * *[Kinesis Data Firehose](https://aws.amazon.com/kinesis/data-firehose/):* Provision, manage and scale compute, memory, and network resources required to load your streaming data.
  * *[Kinesis Video Streams](https://aws.amazon.com/kinesis/video-streams/):* Provides you SDKs you can install on your devices to make it easy and secure to stream video to AWS.

These tools are designed to help manage the velocity and volume of incoming streaming data, giving you access and insight into a rolling, sliding, or tumbling window of data from the stream as it’s produced in real time. This contrasts with conventional data capture and storage paradigms wherein a static, point-in-time view of the data may be obtained using ad-hoc queries.

So then, what if your use case requires management of a high volume of incoming data such as clickstream data, and also a requirement to store elements of this data durably for retrieval over time?  That’s where MongoDB, and more specifically, [MongoDB Atlas](https://www.mongodb.com/cloud/atlas), and [MongoDB Stitch](https://www.mongodb.com/stitch) come in.

### MongoDB Atlas and Stitch
MongoDB Atlas provides all of the features and benefits of MongoDB, without the operational overhead. MongoDB Atlas is available on demand and billed on an hourly basis, letting you focus on the high-value tasks associated with developing and deploying applications, rather than managing and operating the database.

It’s easy to get started. Simply select the instance size, region, and features that meet your application requirements, and within minutes your cluster environment is up and running and ready to be connected to your application.

MongoDB Stitch is the serverless platform from MongoDB that provides a single interface to the database and third-party services.

Stitch offers the following services:


 * *Stitch QueryAnywhere:* Exposes the full power of working with documents in MongoDB and the MongoDB query language, directly from your web and mobile application frontend code. A powerful rules engine lets developers declare fine-grained security policies.

 * *Stitch Functions:* Allows developers to run simple JavaScript functions in Stitch’s serverless environment, making it easy to create secure APIs or to build integrations with microservices and server-side logic. Enables integration with popular cloud services such as Twilio, enriching your apps with a single Stitch method call.

 * *Stitch Triggers:* Real-time notifications that launch functions in response to changes in the database. The functions can make further database changes, push data to other places, or interact with users – such as through push notifications, text messages, or emails.

 * *Stitch Mobile Sync (Coming soon):* Automatically synchronizes data between documents held locally in MongoDB Mobile and the backend database. MongoDB Mobile allows mobile developers to use the full power of MongoDB locally. Stitch Mobile Sync ensures that data is kept up to date across phones and all other clients in real time.


### MongoDB + Atlas + Stitch + Amazon Kinesis = Intelligent, Durable Steams
Let’s look at an example scenario in which a stream of data is being generated as a result of actions users take on a website. 

This could be clickstream data from a web app, log data from a log collection agent, or whatever data is being generated at high volumes, at high velocity. Let’s assume we want to produce streaming analytics on this data in addition to retaining durable, enriched data within MongoDB.  Some examples of this type of processing may include cart abandonment, product recommendations, or even credit card fraud detection.

In our solution, we’ll want to ensure that the data is stored durably and securely, before making the data available for reporting and analytics. Consider the following logical architecture.

![MongoDB and Kinesis](https://i.imgur.com/7XkCmIO.png "Figure 2. MongoDB Kinesis Architecture")

*Figure 2. MongoDB Kinesis Architecture*

MongoDB Stitch, and enables us to do the following:

 * *Store:* Reliably, securely store the incoming data into MongoDB in a collection in MongoDB Atlas.

 * *React:* Trigger on incoming events (updates, inserts, deletes) to the collection, and update an additional set of data in another collection.

 * *Enrich:* Since Stitch gives seamless access to the database and collections in context, it's easy to leverage the data passed from a Stitch Trigger to enrich other collections or documents – producing materialized views or aggregations as data enters the database.

 * *Integrate:* Leverage Stitch Functions to integrate with third-party services, in this case, Kinesis.


### Stitch Triggers
Storing incoming data in MongoDB prior to making it available in a data stream has several advantages. First, we’re ensuring durability. Second, since we’re using MongoDB Atlas, we have the ability to leverage database triggers.

Stitch Triggers differ from traditional database triggers in that they don’t consume resource in the database – they’re run separately in the Stitch application environment. This facilitates enhanced scalability and in the world of real-time, streaming data, scale is incredibly important.

Let’s take a closer look at a Stitch Trigger.

![Stitch Trigger](https://i.imgur.com/Hx8Rl7l.png)
*Figure 3. Stitch Trigger Configuration*

The key elements of configuration for a Stitch Trigger in Stitch include the following:
 * *Name:* This is the name of the trigger – arbitrary. 
 * *Database:*  The name of the database that you’re monitoring for actions to initiate a trigger.
 * *Collection:* The name of the collection that you’re monitoring for events
 * *Operations Type:* The actions you’re monitoring. This includes insert, update, delete, and replace operations. These are also referred to as events, or event types.
 * *Full Document Response:* Optional configuration that tells Stitch to respond to the triggered event by sending the entire document impacted by the operation (update or replace)  This is handy if you want to enrich another collection as a result of the trigger.
 * *Function Name:* The name of the Stitch Function that will be called when the action is triggered.

### Stitch Service Integration
The purpose of this trigger will be to react to incoming data, evaluate this data for a certain condition and then place a record into an Amazon Kinesis Stream.  

To accomplish this integration between MongoDB Atlas and Amazon Kinesis, Stitch provides Third Party Service integration.  There are currently five services that are offered within Stitch. 

 * *[Amazon Service](https://docs.mongodb.com/stitch/reference/partner-services/amazon-s3/):* Used to connect Stitch and MongoDB to any one of the many Amazon services such as S3, SES, SQS, and in this case Kinesis.
 * *[GitHub Service](https://docs.mongodb.com/stitch/reference/partner-services/github/):* The GitHub Service in MongoDB Stitch supports setting up an incoming webhook to consume and process GitHub actions as part of a function. There are no service actions or service rules for the GitHub service.  
 * *[HTTP Service](https://docs.mongodb.com/stitch/reference/partner-services/http/):* The HTTP service in MongoDB Stitch enables you to integrate Stitch apps with any third-party service that provides a REST API over HTTP, such as Slack, or your own custom service.
 * *[Push Notifications (GCM)](https://docs.mongodb.com/stitch/reference/partner-services/push-notifications/):* Integrate mobile (Android and iOS) apps with the Google Cloud Messaging (GCM) service to provide push notifications. You configure and send notification messages from within the Stitch console, while clients register with GCM for messages sent to specific topics.
 * *[Twilio Service](https://docs.mongodb.com/stitch/reference/partner-services/twilio/):* Integrate apps with Twilio’s Programmable SMS service.

In our example, we’ll leverage the AWS Service to integrate our trigger with Amazon Kinesis.  Before we can call the service from a function, we need to configure a few settings for the service such as region, aws access key ID and Secret Access Key.  

![Figure 4. AWS Service Configuration](https://i.imgur.com/HQBU4TP.png)
*Figure 4. AWS Service Configuration*

Once configured, the service may be accessed via calls from Stitch functions. Here’s a function called putRecord.  This is an example of a function referencing our newly configured AWS Service. 


```javascript
exports = function(event){
 const awsService = context.services.get('aws');
 console.log(JSON.stringify(event.fulldocument));
try{
   awsService.kinesis().PutRecord({
     Data: JSON.stringify(event.fullDocument),
     StreamName: "stitchStream",
     PartitionKey: "1"
 	 }).then(function(response) {
 	   return response;
 	 });
}
catch(error){
  console.log(JSON.parse(error));
}
};
```
*Example Stitch Function Calling AWS Kinesis Service*

As part of trigger execution, Stitch will forward details associated with the trigger event, including the full document involved in the event (i.e. the newly inserted, updated, or deleted document from the database and collection.) This is where we can evaluate the condition and decide whether or not to put the record onto a stream.

One of the powerful benefits of leveraging Stitch as a part of your data streaming architecture is that you can continue to rely on MongoDB in the very same manner your used to – letting your application capture data and write it to MongoDB, inserting documents into a collection in MongoDB Atlas. However, since we've configured Stitch Triggers to take action upon each update, insert, or replace we can take advantage of serverless functions without having to write the REST API wrapper code.  This can saves hours, reduces complexity, and streamlines your application development process.

In this case, the function leverages our pre-configured AWS Service and calls the PutRecord method to insert a document into a stream we've created called stitchStream.

Once the record is in the Kinesis Stream you can configure additional services downstream to act on the data.  A common use case incorporates Amazon Kinesis Data Analytics to perform analytics on the streaming data. Amazon Kinesis Data Analytics offers pre-configured templates to accomplish things like anomaly detection, simple alerts, aggregations, and more.

![Amazon Data Analytics - Anomaly Detection Example](https://i.imgur.com/rQYf1qh.png)
*Figure 5. Amazon Data Analytics - Anomaly Detection Example*

### Wrapping Up
I’ve presented information on MongoDB Atlas and the MongoDB Stitch serverless platform, and shown how integrating with Amazon Kinesis can be accomplished to easily reduce complexity and enable you to manage high volumes and velocities of data from your applications. You can begin using MongoDB Atlas for free and see for yourself exactly how easy it is to get started. In my next article, I'll present a bit more detail around exactly how to integrate these components to accomplish a variety of tasks.  

### Resources
#### MongoDB Atlas
 * [Getting Started - Tutorial Playlist](https://www.mongodb.com/presentations/tutorial-series-getting-started-with-mongodb-atlas)
 * [FAQ](https://www.mongodb.com/cloud/atlas/faq)
#### MongoDB Stitch
 * [Getting Started Documentation](https://docs.mongodb.com/stitch/getting-started/)
 * [MongoDB Stitch Tutorials](https://docs.mongodb.com/stitch/tutorials/)
#### Amazon Kinesis
 * [Getting Started](https://docs.aws.amazon.com/streams/latest/dev/getting-started.html)
 * [Kinesis Data Streams](https://aws.amazon.com/kinesis/data-streams/)
 * [Kinesis Data Analytics](https://aws.amazon.com/kinesis/data-analytics/)




