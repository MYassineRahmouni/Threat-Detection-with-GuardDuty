# Threat Detection with GuardDuty



**Author:** yassine rahmouni  
**Email:** yassinerah545@gmail.com

---

![Image](http://learn.nextwork.org/stimulated_turquoise_serene_manatee/uploads/aws-security-guardduty_v1w2x3y4)

---

## Introducing Today's Project!

### Tools and concepts

The services I used were GuardDuty, CloudFormation, S3 , and CloudShell. Key concepts we learnt include SQL injection + command injection, using kinux and malware protection!

### Project reflection

This project took me approximately 2 hours .The most challenging part was setting up the stuck in CloudFormation I got stuck with a lot of errors, so i had to debbug and find a solution it was most rewarding to reveal the victim's protected file as an attacker.

I did this project to discover AWS features and learn about GuardDuty.This project certainly met my excepectation and was a good challange!

---

## Project Setup

To set up this project, I deployed a CloudFormation template that launches an insecure web app (OWASP Juice Shop).The three main components are the web app infrastructure, an S3 bucket and GuardDuty protecting our environment. 

The web app deployed is called OWASP Juice Shop. To practice our GuardDuty skills, we will attack the Juice Shop, and then visit the GuardDuty console to detect and analyze its findings - does it pick up on our attacks to our web app ?

GuardDuty is an AI-powered threat detection service, which means it is desgined to help us find security attacks or vulnerabilities that affects our AWS resources/environment. Once it detects something unusual, it's up to us to investigate.

![Image](http://learn.nextwork.org/stimulated_turquoise_serene_manatee/uploads/aws-security-guardduty_n1o2p3q4)

---

## SQL Injection

The first attack I performed on the web app is SQL injection, which means injecting malicious SQL code that manipulates the result from our web app. SQL injection is a security risk because it can let attackers bypass logins, oe delete/edit data.

My SQL injection attack involved entering a code 'or 1=1;-- into the email field of the web app's login page.
This means the login query will always evaluate to true 
(our database is manipulated into telling our web app this login exists).

![Image](http://learn.nextwork.org/stimulated_turquoise_serene_manatee/uploads/aws-security-guardduty_h1i2j3k4)

---

## Command Injection

Next, I used command injection, which is a tachnique that manipulates the web app's web server to run code that has been enter in a form. The Juice Shop web app is vulnerable to this because it does not sanitize user inputs (does not bloc scripts).

To run command injection ,I entered JavaScript code in the username field of the web app's admin console.  This script will tell the web server to expose the server's IAM credentials and save them in a publicly accessible JSON file.

![Image](http://learn.nextwork.org/stimulated_turquoise_serene_manatee/uploads/aws-security-guardduty_t3u4v5w6)

---

## Attack Verification

To verify tha attack's success, I visited the publicly exposed credentials file. This page showed us access keys that represents our EC2 instance's access to the developer's AWS enviornment.I can use those keys to get the same level of access.

![Image](http://learn.nextwork.org/stimulated_turquoise_serene_manatee/uploads/aws-security-guardduty_x7y8z9a0)

---

## Using CloudShell for Advanced Attacks

The attack continues in CloudShell, because this is a command-line tool I can use to run AWS commands that uses the credentials I've stolen. CloudShell is now our medium for doing suspecious things like stealing data from an S3 bucket.

In CloudShell, I used wget to download the exposed credentials file into our CloudShell enviornment.Next, I ran a command using cat and jq to read the downloaded file and format it nicely - so the credentials (in JSON) are easy to understand.

I then set up a profile using all of the stolen credentials. I had to create a new profile because the hacker doesn't inherently have access to victim's AWS enviornment. I 'll need to use teh profile to switch permission settings.

![Image](http://learn.nextwork.org/stimulated_turquoise_serene_manatee/uploads/aws-security-guardduty_j9k0l1m2)

---

## GuardDuty's Findings

After performing the attack, GuardDuty reported a finding within 15 minutes. Findings are notifications from GuardDuty that somrthing suspecious has happened, and they give you additional details about teh who/what/when of the attack.

GuardDuty's finding was called 
UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS , Which means credentials belonging to an EC2 instance in my enviornment were being used in another account. Anomaly detection was used because this was unusual behaviour.

GuardDuty's detailed finding reported the S3 bucket was affected, the action that was done using the stolen credentials was GetObject; and the EC2 instance whose credentials were leaked. The IP address + location of the actor was also available. 

![Image](http://learn.nextwork.org/stimulated_turquoise_serene_manatee/uploads/aws-security-guardduty_v1w2x3y4)

---

## Extra: Malware Protection

For my project extension, I enabled Malware Protection for S3 .Malware is a file that contains threats e.g. opening the file will cause a data breach or a deletion of resources.

To test Malware Protection, I uploaded an EICAR file into a protected bucket. The uploaded file won't actually cause any damage because the test file in only designed to alert antivirus software.

Once I uploaded the file, GuardDuty instantly triggered called Object:S3/MaliciousFile. This verified that GuardDuty could successfully detect malware. It also mentioned that threat type is EICAR test-file(which means not a virus).

![Image](http://learn.nextwork.org/stimulated_turquoise_serene_manatee/uploads/aws-security-guardduty_sm42x3y4)

---
