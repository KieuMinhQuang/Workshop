---
title: "Week 4 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---


### Week 4 Objectives:

* Learn how to grant applications access to AWS services using IAM.
* Practice using Access Keys and Secret Access Keys to connect a Python application to Amazon S3.
* Explore and practice using IAM Roles for EC2 instances to enhance security and avoid storing credentials within source code.

### Tasks for this week:

| Day | Task | Start Date | Completion Date | Resources |
| --- | --- | --- | --- | --- |
| Mon | Prepare the practice environment by creating an EC2 instance (Amazon Linux) and an S3 bucket via the AWS Console. | 24/08/2026 | 24/08/2026 | <https://000048.awsfcaj.com/> |
| Tue | Learn to create an IAM user, grant S3 access permissions, and generate Access Keys and Secret Access Keys for application authentication. | 25/08/2026 | 25/08/2026 | <https://000048.awsfcaj.com/> |
| Wed | Use Access Keys in a Python application (via the Boto3 library) to upload files to S3; examine the security risks of hardcoding credentials directly into source code. | 26/08/2026 | 26/08/2026 | <https://000048.awsfcaj.com/> |
| Thu | Learn to create an IAM role, attach it to an EC2 instance, and use it to grant the application access to S3 without needing to specify Access Keys in the source code. | 27/08/2026 | 27/08/2026 | <https://000048.awsfcaj.com/> |
| 6 | Verify the results of uploading files to S3 using an IAM Role, explore the mechanism for issuing temporary credentials via AWS STS, and practice cleaning up AWS resources following the workshop. | 28/08/2026 | 28/08/2026 | <https://000048.awsfcaj.com/> |

### Achievements in Week 4:

* Gained an understanding of how to prepare a hands-on environment and establish a connection between an EC2 instance and Amazon S3.
* Practiced creating an IAM user and access keys, and used the Boto3 library to enable a Python application to interact with S3.
* Recognized the security risks associated with hardcoding access keys and secret access keys directly into source code.
* Practiced creating and assigning an IAM role to an EC2 instance, allowing an application to access AWS services using temporary credentials.
* Learned to distinguish between authentication methods using access keys versus IAM roles, understood the security benefits of IAM roles, and followed the procedure for cleaning up AWS resources.