

# Threat Detection with GuardDuty

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-guardduty)

**Author:** Albert  
**Email:** tapcyberx@gmail.com

---

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-guardduty_sm42x3y4)

---

## Introducing Today's Project!

### Tools and concepts

The services I used were GuardDuty, CloudFormation, S3, and CloudShell. Key concepts I learnt include SQL + Command Injection, using Linux commands like wget, cat and jq, and malware protection.

### Project reflection

This project took me approximately 4H. The most challenging was the part of the Linux command in CloudShell. It was most rewarding to reveal the victim's protected file as attacker.

I did this project to deepen my understanding of cloud security and how threat detection works in real-world AWS environments.

Using Amazon GuardDuty exceeded my expectations it provided clear, actionable insights into simulated attacks. This project gave me valuable hands-on experience with both the attacker’s perspective and the defensive capabilities AWS offers.

It was a great learning opportunity that strengthened my knowledge of securing cloud infrastructure.

---

## Project Setup

To set up this project, I deployed a CloudFormation template that launches an insecure web application specifically, the (OWASP Juice Shop).

The setup includes three main components:
. The web app infrastructure
. An S3 bucket
. Amazon GuardDuty for monitoring and protecting the environment

This environment allows me to simulate real-world attack scenarios and observe how GuardDuty detects and responds to threats.

The deployed web app is OWASP Juice Shop, intentionally designed to be vulnerable. I’ll use it to practice my GuardDuty skills by launching an attack against the app, then visiting the GuardDuty console to see if the activity is detected.

This will help me analyze the findings, understand how GuardDuty logs suspicious behavior, and test how effective it is at identifying real-world web threats in an AWS environment.

AWS GuardDuty is an AI threat detection service, which means it helps you find potential security risks or attacks in your apps and AWS environment.
It uses machine learning to look for unusual activity in your AWS account, like your network traffic and CloudTrail activity logs. If it finds something suspicious, it will alert me so you I can investigate.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-guardduty_n1o2p3q4)

---

## SQL Injection

The first attack I performed on the web app was a SQL injection is a technique where an attacker inserts malicious SQL code into an input field to manipulate the application's database query.

SQL injection is a serious security vulnerability because it can allow attackers to bypass authentication, access unauthorized data, or even modify or delete database records. It’s one of the most common and dangerous web vulnerabilities listed in the OWASP Top 10.



For my SQL injection attack, I entered the code ' OR 1=1;-- into the email field on the web app’s login page.

This input manipulates the SQL query so that the condition always evaluates as true, tricking the database into thinking the login is valid. As a result, I was able to bypass authentication without knowing any real user credentials.

This is a classic example of how SQL injection can be used to exploit weak input validation in web applications.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-guardduty_h1i2j3k4)

---

## Command Injection

Next, I performed a command injection attack, which involves tricking the web app’s server into executing unauthorized system commands entered by the user often through a form or input field.

The Juice Shop web app is vulnerable to this type of attack because it doesn’t properly sanitize user inputs, allowing malicious commands to be passed directly to the underlying operating system.

This type of vulnerability can give attackers access to server files, processes, or even remote shells, making it a serious security risk.

To run command injection, I entered JavaScript code in the username field of the web app's admin console.
This script will tell our web server to expose the server's IAM credentials and save them in a publicly accessible JSON file.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-guardduty_t3u4v5w6)

---

## Attack Verification

To verify the success of my attack, I navigated to the publicly exposed credentials.json file within the web app. This file displayed AWS access keys tied to the EC2 instance running the application.

Seeing those credentials confirmed that the attack worked and anyone with access to that file could use the keys to gain the same level of permissions as the EC2 instance itself, potentially accessing or modifying resources in the AWS environment.




![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-guardduty_x7y8z9a0)

---

## Using CloudShell for Advanced Attacks

I’m using AWS CloudShell to continue the attack because it provides a command-line interface where I can run commands using the stolen credentials.

CloudShell acts as my medium for simulating malicious activity, such as accessing and stealing data from an S3 bucket, all within the AWS environment.

Then, I ran cat along with jq to read and format the contents of the file. Using jq made the JSON output easy to understand, allowing me to clearly see the stolen access keys and secret key values.

This step helps me to simulates how an attacker could extract and prepare stolen credentials for use in further malicious actions.

I created a new AWS CLI profile to configure and use the stolen credentials.

Since an attacker wouldn’t naturally have access to the victim’s AWS environment, setting up a new profile allows me to authenticate as the compromised user. 
This lets me switch permission contexts and perform actions with the same level of access as the EC2 instance tied to those credentials.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-guardduty_j9k0l1m2)

---

## GuardDuty's Findings

After performing the attack, GuardDuty reported a finding within 15 minutes.
Findings are notifications from GuardDuty that something suspicious has happened, and they give you additional details about the who/what/when of the attack.

GuardDuty's finding was called UnauthorizedAccess: IAMUser/InstanceCredentialExfiltration. Inside AWS, which means credentials belonging to my EC2 instance were being used in another account.
Anomaly detection was used because this was unusual behavior.

GuardDuty's detailed finding reported that an S3 bucket was affected, the action that was done using the stolen credentials (GetObject), and the EC2 instance whose credentials were leaked. 
The IP address + location of the actor was also available.

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-guardduty_v1w2x3y4)

---

## Extra: Malware Protection

For my project extension, I enabled Malware Protection for S3. Malware is a file that contains threats or harm your computer. e.g. opening the file will cause a data breach or a deletion of resources.

To test Malware Protection, I uploaded an EICAR test file into a protected bucket. 
The uploaded file won't actually cause damage because the test file is only designed to alert antivirus software.

Once I uploaded the file, GuardDuty instantly triggered a finding called Object:S3/MaliciousFile.
This verified that GuardDuty could successfully detect malware. It also mentioned that the threat type is EICAR Test File (which means not a virus).

![Image](http://learn.nextwork.org/delighted_indigo_timid_orc/uploads/aws-security-guardduty_sm42x3y4)

---
