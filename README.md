<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Security Monitoring System

**Author:** Gustavo Balaguera  
**Email:** gustavobalaguera214@gmail.com

---

![Image](http://learn.nextwork.org/authentic_brown_happy_nectarine/uploads/aws-security-monitoring_reghtjy)

---

## Introducing Today's Project!

In this project, I will demonstrate how to build a powerful monitoring system using AWS to strengthen security and protect sensitive data. I'm doing this project to learn how to track and respond to access events effectively—an essential skill for roles like Security Engineer, DevOps Engineer, and Systems or Cloud Administrator.
Through this project, I will:
   - Set up AWS CloudTrail to monitor secret access events.
   - Use AWS CloudWatch to log access attempts and trigger notifications.
   - Create SNS alerts to receive notifications when secrets are accessed.
   - Build and compare a second notification system to evaluate security effectiveness.
By the end, I will gain hands-on experience in securing cloud environments and designing real-time security alert systems. 


### Tools and concepts

Services I used were AWS Secrets Manager, CloudTrail, CloudWatch Logs, CloudWatch Alarms, and Amazon SNS to build a security monitoring system.

Key concepts I learned include secure secret storage, event tracking, automated alerts, and troubleshooting cloud-based monitoring setups. I gained hands-on experience in detecting unauthorized secret access, refining alarm settings, and integrating notification systems to enhance cloud security. This project demonstrated how AWS services work together to protect sensitive data and provide real-time security insights.

### Project reflection

This project took me about 2 hours to complete.

---

## Create a Secret

Secrets Manager is a secure AWS service designed to store and manage sensitive information, such as passwords, API keys, and credentials. It provides automatic encryption and secret rotation, ensuring that confidential data remains protected without manual updates.

You could use Secrets Manager to securely store database credentials, API keys, and other sensitive information, reducing the risk of exposure. Instead of hardcoding secrets into your applications or sharing them through unsecured channels, Secrets Manager centralizes secret storage and controls access through AWS IAM policies.

To set up for my project, I created a secret called TopSecretInfo that contains a key-value pair. The key is "The Secret is", and the value is “rice is the best carb”. This secret is stored in AWS Secrets Manager, ensuring it remains secure and accessible for monitoring.

By using Secrets Manager, I can manage sensitive data efficiently while leveraging AWS security features like encryption and controlled access policies. This setup will serve as the foundation for tracking access attempts and enhancing cloud security. 

![Image](http://learn.nextwork.org/authentic_brown_happy_nectarine/uploads/aws-security-monitoring_o5p6q7r8)

---

## Set Up CloudTrail

CloudTrail is a monitoring service that records activity across your AWS account, capturing details like who performed an action, when it happened, and where it originated. It provides valuable insights for security, troubleshooting, and compliance, helping detect unusual behavior, track changes, and maintain regulatory standards.

I set up a trail to record and store account activity, ensuring that interactions are logged securely. By configuring CloudTrail to track access to Secrets Manager, I can identify unauthorized actions, investigate incidents, and strengthen security monitoring. This setup forms a critical part of my AWS security strategy. Time to configure it!

CloudTrail events include Management, Data, Insights, and Network activity events, each tracking different AWS activities.
   - Management events log administrative actions, such as accessing secrets or modifying IAM policies.
   - Data events capture interactions with resources, like uploading files to S3.
   - Insights events detect unusual patterns in account activity.
   - Network activity events track changes in VPC configurations and traffic.

These logs help with security, auditing, and compliance, ensuring visibility into important AWS operations.

### Read vs Write Activity

Read API activity involves viewing information without making changes, such as listing S3 buckets or describing EC2 instances. Write API activity involves modifying or retrieving sensitive data, including creating, deleting, or accessing a secret’s value.

For this project, we need both Read and Write API activity enabled to track secret access effectively. Read will log metadata views, while Write ensures retrieval of the secret value is recorded for security monitoring. This distinction helps detect unauthorized access attempts and strengthens cloud security.

---

## Verifying CloudTrail

I retrieved the secret in two ways:
   - First, through AWS Secrets Manager console, where I navigated to my TopSecretInfo secret and selected Retrieve secret value to view its content.
   - Second, using AWS CLI via CloudShell, where I ran the get-secret-value command to fetch the secret’s value in JSON format.

By testing both methods, I ensured CloudTrail logs these access events, strengthening my ability to track and monitor secret usage. 


To analyze my CloudTrail events, I visited the Event history page in the CloudTrail console and filtered by Event source: secretsmanager.amazonaws.com. I found GetSecretValue events, confirming that CloudTrail successfully logged the retrieval of my secret.

This tells me that my monitoring system is working as expected, capturing access attempts and ensuring I can track who accessed sensitive data. With this visibility, I can detect unauthorized access and strengthen my cloud security strategy!


![Image](http://learn.nextwork.org/authentic_brown_happy_nectarine/uploads/aws-security-monitoring_s8t9u0v1)

---

## CloudWatch Metrics

CloudWatch Logs is an AWS service that collects, stores, and analyzes log data from various AWS resources, including CloudTrail. It's important for monitoring because it provides visibility into system activity, helps troubleshoot issues, and enables real-time alerts for critical security events like secret access.

By integrating CloudTrail logs into CloudWatch Logs, I can track who accessed my secret, detect anomalies, and trigger automated responses when suspicious activity occurs. This setup strengthens security by ensuring I can respond swiftly to potential threats.

CloudTrail's Event History is useful for quick investigations of recent events, providing a record of management actions over the past 90 days.

While CloudWatch Logs are better for long-term storage and advanced monitoring, allowing custom alerts, automated responses, and powerful filtering to focus on critical security events. By storing logs in CloudWatch, I can track secret access over time and proactively detect suspicious activity.

A CloudWatch metric is a measurement used to track specific patterns in log data.

When setting up a metric, the metric value represents the action being tracked—in this case, a secret access event. We set it to 1 so that each time someone retrieves the secret, the counter increases.

Default value is used when no matching events occur in a given period. Setting it to 0 ensures we capture both active and inactive periods, providing a complete view of secret access trends. This helps with security monitoring and alerting. 

![Image](http://learn.nextwork.org/authentic_brown_happy_nectarine/uploads/aws-security-monitoring_a9b0c1d2)

---

## CloudWatch Alarm

A CloudWatch alarm is a monitoring tool that triggers notifications based on metric thresholds.

I set my CloudWatch alarm threshold to Greater/Equal to 1 so the alarm will trigger when my SecretIsAccessed metric detects at least one secret access attempt within a 5-minute period.

This ensures I receive instant alerts whenever my secret is retrieved, helping me track access events and respond quickly to potential security threats. 

I created an SNS topic along the way. An SNS topic is a messaging channel that allows AWS services to send notifications to multiple subscribers at once. Instead of sending individual alerts, it distributes messages efficiently to email addresses, SMS numbers, or even applications.

My SNS topic is set up to send an email notification whenever CloudWatch detects that my secret is accessed. This ensures I receive an alert the moment a sensitive event occurs, helping me respond quickly to potential security threats.

AWS requires email confirmation because it ensures that only the intended recipient subscribes to SNS notifications and prevents unauthorized subscriptions.

This helps prevent spam, accidental subscriptions, and security risks, making sure that alerts are sent only to verified users. By confirming my email, I ensure that I receive important notifications about secret access events without unwanted interruptions. 

![Image](http://learn.nextwork.org/authentic_brown_happy_nectarine/uploads/aws-security-monitoring_fsdghstt)

---

## Troubleshooting Notification Errors

To test my monitoring system, I retrieved the secret value again in AWS Secrets Manager, expecting the CloudWatch alarm to trigger and send an SNS email notification.

The results were unexpected—no email was received, meaning the alarm or SNS setup needs troubleshooting. I’ll check the CloudWatch alarm status, SNS subscription confirmation, and CloudTrail logs to ensure everything is correctly configured. Once resolved, my monitoring system will successfully alert me whenever the secret is accessed. 

To test my monitoring system, I successfully set up CloudTrail, CloudWatch Logs, and SNS but noticed the CloudWatch alarm wasn’t triggering notifications as expected.

I manually triggered the alarm using the AWS CloudShell command to set its state to ALARM, but no email arrived. This confirmed that the issue wasn’t with CloudTrail or SNS itself, but rather with how the alarm was being evaluated.

To troubleshoot, I verified the alarm’s state, ensured my SNS subscription was confirmed, checked the exact alarm name in my command, and waited for possible delays.

Finally, I adjusted the CloudWatch alarm settings by changing Statistic from Average to Sum, ensuring secret access events were counted correctly. I also reduced the Period from 5 minutes to 1 minute for faster notifications. With these updates, my monitoring system should now trigger email alerts reliably!

I initially didn't receive an email before because my CloudWatch alarm was not evaluating secret access events correctly. The alarm was set to use Average instead of Sum, meaning secret access events weren’t crossing the threshold for triggering notifications.

The key solution was changing the alarm’s Statistic setting to Sum so every secret access counted properly. Additionally, reducing the Period from 5 minutes to 1 minute ensured faster detection and notification delivery.

---

## Success!

To validate that my monitoring system works, I retrieved the secret value again in AWS Secrets Manager, triggering an event that CloudTrail recorded.

I checked the CloudWatch console and confirmed that my Secret is accessed alarm entered the In alarm state, verifying that the metric filter correctly detected the access.

I received an email notification from AWS SNS with the subject "ALARM: SecretIsAccessedAlarm", confirming that the monitoring system successfully alerted me to the secret access event. 

![Image](http://learn.nextwork.org/authentic_brown_happy_nectarine/uploads/aws-security-monitoring_ageraergearge)

---
