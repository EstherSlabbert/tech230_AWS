# Amazon CloudWatch

We use monitoring to determine why things happened, what (which metrics) changed and what we can do to improve functionalities and reduce unnecessary costs. We can use the statistics to make adjustments.

![CloudWatch](https://docs.aws.amazon.com/images/AmazonCloudWatch/latest/monitoring/images/CW-Overview.png)

[How Amazon CloudWatch works](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_architecture.html)

[Latest on CloudWatch Alarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/US_AlarmAtThresholdEC2.html)

## Create a Dashboard

1. Run an EC2 instance.
2. In EC2 'Monitoring' tab at the bottom of the page > 'Manage detailed monitoring' > enable detailed monitoring (costs more, gives minute-by-minute monitoring).

![Monitoring](/images/monitor.png)
![Enable Monitoring](/images/monitor1.png)

3. In EC2 'Monitoring' tab at the bottom of the page > 'Add to dashboard' > Select a dashboard/create dashboard (name and create a dashboard).

![Select Create Dashboard](/images/dashboard.png)
![Create & Name Dashboard](/images/create-dashboard.png)
![Select Dashboard](/images/dashboard1.png)

This is what your dashboard could look like:

![Dashboard View](/images/dashboard2.png)

## Set up an alarm for CPU usage

1. Navigate to 'CloudWatch' on AWS > Select 'Alarms' > 'All Alarms'. Then click 'Create Alarm'.

![CloudWatch homepage](/images/cw1.png)
![Create Alarm](/images/cw2.png)


2. Rename the Metric graph. Then scroll down to the 'Browse Tab' of metrics and Search for 'EC2', then select 'EC2 > Per Instance Metrics'.

![Rename the Metric graph](/images/cw3.png)
![Browse Metrics](/images/cw4.png)

3. Search for your EC2 instance using its instance ID, then select the desired metric (in this case 'CPUUtilization') and click 'Select Metric'.

![Select EC2 metric](/images/cw5.png)

4. It will take you to a page that looks like this (Your graph will not have a red dotted line yet it will appear after you make a specification further down the page):

![Step 1](/images/cw7.png)

Enter your desired value for CPUUtilization (in this case I have specified 1%).

![Step 1 metric](/images/cw6.png)

Then click 'Next'.

5. Select 'Create new topic' and name the topic and specify the email address you wish to have the alarm notifications sent to. Then click create/select topic.

![Step 2](/images/cw8.png)

Should automatically change to look like this:
![Step 2](/images/cw8-1.png)

There are other options which you may wish to change as you scroll further down, but I have not changed them:
![Step 2](/images/cw9.png)

Click 'Next'.

6. Name the alarm something appropriate and give it a description. Then click 'Next'.

![Step 3](/images/cw10.png)

7. You should be taken to a page where you can preview all of the configurations you have chosen. Review them, then when you are satisfied click 'Create alarm' ![Create Alarm](/images/cw11.png).
8. You should get the following notifications:

![Notifications](/images/cw12.png)

And a subscription confirmation email, which you will need to follow the link in the email to confirm this set up:

![Email1](/images/cw-confirm.png)

Once you click the link it should take you to a page that looks like this:

![Subscription confirmation](/images/cw-confirm1.png)

9. If the alarm is triggered - by the CPUUtilization going above the specified range - according to the configurations set you should get an email that looks something like this:

![Email2](/images/alarm.png)
