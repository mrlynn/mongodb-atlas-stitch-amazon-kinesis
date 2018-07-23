
# MongoDB Atlas, Stitch and Amazon Kinesis
## Integrating MongoDB and Amazon Kinesis

### Streaming Data
Every day, millions of people visit websites and interact with applications all across the Internet. Their virtual footprints tell a story that can be incredibly valuable. As users interact with the content clicking links, viewing products and maybe event purchasing a product, the stream of click data is compiled and sent to a log file, stored in a database or possibly added to a data stream. This is just one example of streaming data. There are many other examples, across virtually every industry. 

Stream processing differs from batch processing in the speed with which you’re able to derive insight, and therefore value from the data. With batch oriented data collection and reporting, processing is performed on a periodic basis typically with gaps between execution cycles. With stream processing, streaming data is analyzed in real-time using the concept of windows into the data. 

More and more businesses are leveraging modern tools to analyze streaming data in real-time rather than having to wait for the data to settle into batches.  This is because over time, the insights become stale and lose value.  Consider the example of streaming transactions in a credit card processing use case.  The value of analyzing the data and detecting anomalies is extremely high within seconds, and minutes of the event whereas, in several days, there’s likely virtually zero value in that specific data.

![Value of Data Over Time](https://i.imgur.com/K21Vrl1.png "Figure 1. Value of Data Over Time")
*Figure 1. Value of Data Over Time*

In this article, I’ll discuss some possible use cases for stream processing, some tools commonly used to help derive value from the data and a potential architecture that combines the best features of streaming data management in addition to storing the data durably without incurring the operational overhead associated with deploying and managing an on-premise database solution.

### Amazon Kinesis
Streaming solutions such as Amazon Kinesis enable you to minimize the time and cost associated with capturing and taking action on high volumes of incoming data.

There are four primary products in the Kinesis family:
  * *Kinesis Data Streams:* Reliably capture Click-stream, IoT, or other data at high volume and scale.
  * *Kinesis Data Analytics:* Process and analyze streaming data using standard SQL.
  * *Kinesis Data Firehose:* Provision, manage and scale compute, memory, and network resources required to load your streaming data.
  * *Kinesis Video Streams:* Provides you SDKs you can install on your devices to make it easy and secure to stream video to AWS.

These tools are designed to help manage the velocity and volume of incoming streaming data, giving you access and insight into a rolling, sliding or tumbling window of data from the stream as it’s produced in real-time. This contrasts with conventional data capture and storage paradigms wherein a static, point in time view of the data may be obtain using ad-hoc queries.

So then, what if your use case requires management of a high volume of incoming data such as clickstream data, and also a requirement to store elements of this data durably for retrieval over time?  That’s where MongoDB, and more specifically, MongoDB Atlas, and Stitch come in.

### MongoDB Atlas and Stitch
[MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
 provides all of the features and benefits of MongoDB, without the operational overhead required when you run MongoDB on your own. MongoDB Atlas is available on demand through and billed on an hourly basis, letting you focus on the high value tasks associated with developing and deploying applications rather than managing and operating the database.

It’s easy to get started. Simply select the instance size, region, and features that meet your application requirements and within minutes your cluster environment is up and running and ready to be connected to your application.

[MongoDB Stitch](https://www.mongodb.com/stitch) is a Backend-as-a-Service (BaaS) that provides a single API to the database and third party services.

MongoDB Stitch is built into MongoDB Atlas and provides a serverless platform that’s integrated seamlessly into MongoDB Atlas. The platform offers the following set of services:

 * MongoDB Database Access: Provides seamless integration with your MongoDB Databases and Collections via a set of built-in services and SDK’s (IoS, Javascript, and Android.)
 * Third Party Service Integration: Service calls and database actions can be composed with each other inside functions. Here we use data from MongoDB to decide which users to send a text to.
 * Integrated Access Rules: Declaratively control which fields a user can access by defining simple JSON rules or JavaScript functions. Grant others access to approved aggregations while maintaining data privacy.
 * Functions: Run your JavaScript code server-side, within the Stitch backend. Stitch transparently scales to meet your usage.

### MongoDB + Atlas + Stitch + Amazon Kinesis = Intelligent, Durable Steams
Let’s look at an example scenario in which a stream of data is being generated as a result of actions users take on a web site. 

This could be clickstream data from a web app, log data from a log collection agent, or whatever data is being generated at high volumes, at high velocity. Let’s assume we want to produce streaming analytics on this data in addition to retaining durable, enriched data within MongoDB.  Some examples of this type of processing may include Cart Abandonment, Product Recommendations or even Credit Card Fraud detection.

In our solution, we’ll want to ensure that the data is stored durably, and securely before making the data available for reporting and analytics. Consider the following logical architecture.

![MongoDB and Kinesis](https://i.imgur.com/h0lzjAD.png "Figure 2. MongoDB Kinesis Architecture")
*Figure 2. MongoDB Kinesis Architecture*

MongoDB Stitch, the serverless platform is built into MongoDB Atlas and enables you to do the following:

 * *Store:* Reliably, securely store the incoming data into MongoDB in a Database and Collection in MongoDB Atlas.
 * *React:* Trigger on incoming events (updates, inserts, deletes) to the database, and collection and update an additional set of data in another collection.
 * *Enrich:* Since Stitch gives seamless access to the database and collections in context, its easy to leverage the data passed from a database event trigger to enrich other collections or documents -- producing materialized views or aggregations as data enters the database.
 * *Integrate:* Leverage serverless functions to integrate with third party services, in this case Kinesis.

### Stitch Database Triggers
Storing incoming data in MongoDB prior to making it available in a data stream has several advantages. First, we’re ensuring durability. Second, since we’re using MongoDB Atlas, we have the ability to leverage database triggers.

[Triggers](https://docs.mongodb.com/stitch/mongodb/triggers/) in stitch differ from traditional database triggers in that they don’t consume resource in the database - they’re run separately in the Stitch application environment.  This facilitates enhanced scalability and in the world of real-time, streaming data, scale is incredibly important.

Let’s take a closer look at a Stitch Trigger.

![Stitch Trigger](https://i.imgur.com/Hx8Rl7l.png)
*Figure 3. Stitch Trigger Configuration*

The key elements of configuration for a Database Trigger in Stitch include the following:
 * *Name:* This is the name of the function - arbitrary. You’ll call this from code if you’re leverage the SDK from a Stitch enabled App.
 * *Database:*  The name of the database where you’re monitoring for actions to initiate a trigger.
 * *Collection:* The name of the collection where you’re monitoring for actions.
 * *Operations Triggered:*  The actions you’re monitoring. This includes Inserts, Updates, Deletes, and Replace operations. These are also referred to as Events, or Event Types.
 * *Full Document Response:* Optional configuration that tells Stitch to response to the triggered event by sending the entire document impacted by the operation (insert, replace, etc.)  This is handy if you want to enrich another collection as a result of the trigger.
 * *Function Name:* The name of the Stitch function that will be called when the operation or action is triggered.


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

Once configured, the service may be accessed via calls from Stitch serverless functions. Here’s an example of a function referencing our newly configured AWS Service. 

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

One of the powerful benefits of leveraging Stitch as a part of your Data Streaming architecture is that you can continue to rely on MongoDB in the very same manner your used to. Letting your application capture data and write it to MongoDB in the same manner it would normally, inserting documents into a collection in a database on MongoDB Atlas. However, since we've configured Database Triggers to take action upon each Update, Insert, or Replace we can take advantage of serverless functions without having to write the REST API wrapper code.  This can saves hours, reduce complexity and streamlines your application development process.

In this case, the function leverages our pre-configured AWS Service and calls the `PutRecord` method to insert a document into a stream we've created called `stitchStream`.

Once the record is in the Kinesis Stream you can configure additional services downstream to act on the data.  A common use case incorporates Amazon Data Analytics to perform analytics on the streaming data. Amazon Data Analytics offers pre-configured analytics templates to accomplish things like anomaly detection, simple alerts, aggregations and more.

![Amazon Data Analytics - Anomaly Detection Example](https://i.imgur.com/rQYf1qh.png)
*Figure 5. Amazon Data Analytics - Anomaly Detection Example*

### Wrapping Up
We've presented a discussion of MongoDB Atlas, Stitch Backend as a Service and shown how integrating with Amazon Kinesis can be accomplished to easily reduce complexity and enable you to manage high volumes and velocities of data from your applications. You can begin using MongoDB Atlas for Free and see for yourself exactly how easy it is to get started. In my next article, I'll present a bit more detail around exactly how to integrate these components to accomplish a variety of tasks.  


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




