# Hands-on with CloudFormation and CodePipeline

credit: [“AWS Realtime Web Analytics Workshop”](https://github.com/aws-samples/realtime-web-analytics-workshop) from AWS Samples

## Initial deployment

1) Go to [module 1](https://github.com/aws-samples/realtime-web-analytics-workshop/blob/master/module-1/README.md) and do the chapter 1. Deploy Web Servers and Kinesis Resources using CloudFormation Template

## Do the rest of Module 1 -- Configure a fleet of Web Servers to send Clickstream data to a Kinesis Firehose delivery stream

Over next 20 minutes, you are tasked to visit following page and install a CloudWatch
dashboard for web operations. This time should be enough to complete modules 1, 2 and 3.

If you have extra time, then please consider to complete module 4 as well.

2) Make a local copy of lab files

```shell
$ git clone --depth 1 https://github.com/aws-samples/realtime-web-analytics-workshop.git
$ cd realtime-web-analytics-workshop
```

3) Do the remaining chapters of module 1.

You can get the python command that is used for traffic generation from
CloudFormation Outputs tab. Paste it in a terminal and run it.

```shell
$ cd module-1
$ <pasted-command-here>
```

Go to CloudFormation Resources and check the `AnalyticsBucket` so that you are sure that
some data is streamed there.

### Module 2 -- Configure Kinesis Analytics to Process Clickstream Data

4) Select the 1.A option and update the CloudFormation stack with https://s3-us-west-2.amazonaws.com/realtime-analytics-workshop/2-kinesis-analytics-module-update.yaml

5) As explained in instructions, starting in Kinesis console you will go to the SQL Editor
and then define a new stream. Do this with a copy-paste of the statement provided in instructions.

6) If you have been quick enough on previous steps, than take a couple of minutes to inspect Kinesis
streams from the tabs 'Source data' and 'Real-time analytics' in the console.

7) Connect the stream to the Lambda function that is named `realtime-analytics-workshop-ProcessMetricsFunction` from the in-application stream named `DESTINATION_SQL_STREAM` and with the IAM role `realtime-analytics-workshop-KinesisAnalyticsRole`. Hit the button 'Save and continue'.

8) Skip this validation step and move swiftly to the next module.

### Module 3 -- Visualizing Metrics using CloudWatch Dashboards

9) Update the CloudFormation stack `realtime-analytics-workshop` with https://s3-us-west-2.amazonaws.com/realtime-analytics-workshop/2-kinesis-analytics-module-update.yaml

10) Trigger the publication of metrics by connecting DynamoDB stream with the appropriate
Lambda function. Go to Lambda console, pick up `realtime-analytics-workshop-PublishMetricsFunction`,
and add a DynamoDB trigger. Scroll down so as to display trigger attributes.
Select the table `realtime-analytics-MetricDetails`, a batch size of 300, and use
Trim horizon for the starting position. Click on the 'Add' button (at the bottom),
then on the 'Save' button (at the top).

11) Go to the CloudWatch console for data visualization. Select 'Metrics' from the left menu.
In the bottom half of the screen, click on the tab 'All metrics'.

    Then from the 'Custom namespaces', select 'EventCount', then 'Metrics with no dimensions'.
    Check the box near `clicks`.

    Then click on the word 'All' just below the 'All metrics' tab. Select 'EventAnomaly',
    then 'Metrics with no dimensions'. Check the box near `clicks`.

    At this stage the top half of the console should display two metrics. Click on the
    tab 'Graphed metrics (2)'. Move the vertical axis for anomalies to the right of the graph
    by clicking on the right arrow in the row 'EventAnomaly'. Change graph sampling from 5 minutes
    to 10 seconds.

    Skip the rest of instructions on alarms for this chapter, and move directly to the next chapter.

12) Launch the deployment of a new CloudWatch stack in the same region. After that,
visit the CloudWatch console again, click on 'Dashboards' in the left menu, and
select the new dashboard. Congratulations!
