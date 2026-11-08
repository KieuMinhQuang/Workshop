---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---


# BLOG 3: INTEGRATING PROCUREMENT WORKFLOWS WITH AWS MARKETPLACE VIA APIS

### Building an Automated Software Procurement System Using AWS Marketplace APIs and Serverless Architecture

Hello everyone! Today, I’d like to share an interesting AWS solution for integrating software procurement workflows into enterprise systems. By leveraging AWS Marketplace APIs, organizations can search for products, manage agreements, subscribe to services, and track costs directly from their own internal platforms—eliminating the need to constantly access the AWS Management Console.

#### 3.3.1 The Challenge

In large enterprises, the process of searching for, selecting, and managing software typically involves multiple departments, such as IT, Finance, and Procurement.

If every task must be performed manually within the AWS Management Console, users end up spending significant time searching for products, comparing prices, managing contracts, and consolidating cost reports.

Consequently, enterprises require a solution that integrates these procurement activities into their existing operational workflows.

#### 3.3.2 The AWS Solution

![MP-Buyer Portal architecture on AWS](/Workshop/images/Blog3/mp-buyer-portal-architecture.png)

*Figure 3.3: Serverless architecture of the MP-Buyer Portal integrated with AWS Marketplace APIs.*

AWS offers the "MP-Buyer Portal"—a serverless reference application designed to integrate AWS Marketplace functionalities into a custom web interface.

The solution utilizes the following key components:

- **AWS Marketplace Discovery API:** Facilitates product searches, filtering, and the comparison of software options.
- **AWS Marketplace Agreement API:** Manages contracts, pricing information, and product subscription workflows.
- **Amazon CloudFront and Amazon S3:** Handle the distribution and hosting of the web interface.
- **Amazon API Gateway and AWS Lambda:** Manage incoming requests and process procurement-related operations. - **Amazon DynamoDB:** Stores and synchronizes contract data to facilitate faster report retrieval.
- **Amazon Cognito:** Handles user authentication and secures system access.
- **Amazon EventBridge:** Automatically triggers the data synchronization process every 6 hours.

Additionally, the system can incorporate Strands Agents and Amazon Bedrock to analyze data, generate reports, and provide cost-optimization recommendations.

#### 3.3.3 Benefits and Security

A key highlight of this solution is its ability to integrate procurement processes into internal systems, making it more convenient for users to search for products, manage subscriptions, and track expenses.

Furthermore, the serverless architecture reduces the need for infrastructure management and optimizes costs during periods of system inactivity.

Regarding security, the solution utilizes Amazon Cognito for user authentication, HTTPS via CloudFront, private S3 buckets, and the principle of least privilege (via IAM) to restrict access. For actual deployments, it is also recommended to incorporate AWS WAF, implement CORS restrictions, and configure CloudWatch alerts.

#### 3.3.4 Personal Assessment & Conclusion

In my view, this is a highly useful solution for businesses looking to automate procurement and software management processes. Leveraging AWS Marketplace APIs minimizes manual tasks and enables the integration of purchasing functionality directly into the systems a business already uses.

Through this article, I have gained a better understanding of how to combine Amazon CloudFront, API Gateway, Lambda, DynamoDB, and Cognito to build a complete serverless application. It has also provided an opportunity to delve deeper into API integration, data management, and security within a cloud computing architecture.

*Author: Kieu Minh Quang*

**References:** [AWS – How You Can Embed Procurement into Your Workflows with AWS Marketplace APIs](https://aws.amazon.com/vi/blogs/awsmarketplace/how-you-can-embed-procurement-into-your-workflows-with-aws-marketplace-apis/)