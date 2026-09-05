# Draft-IAM-Customer-Servicing-Portal

## Table of Contents ##

## Overview ##

Diversey Bank is a fictional financial institution headquartered in Chicago, Illinois. Diversey Bank offers a variety of consumer banking solutions to the Midwest region. This lab has built an internal customer-servicing application called Cicero, which is used by bank staff to service customer accounts. Portions of the application, mainly the front-end, have been "vibe coded" to quickly mock the website so that more time can be allotted to the core intent. This lab will not deep-dive into the security configuration of the infrastructure components as that is out-of-scope for the IAM intent. If that is desired, however, check out [AWS-Security-Build](https://github.com/BrandonVolker/AWS-Security-Build). The AWS Free Tier was leveraged for the deployment of services when available.

## Lab Goal ##
This lab seeks to demonstrate a basic competency of the Identity and Access Management (IAM) best-practices that may be implemented to securely grant user access to a web application that contains sensitive customer data that must be protected, such as the Principle of Least Privilege and Just-in-time (JIT) access.

**The Principle of Least Privilege:** is a computer security concept and practice that gives users limited access rights based on the tasks necessary to their job.
**Just-in-time (JIT) access:** is a dynamic, on-demand approach to access control that grants human and non-human identities permissions to an application or system only when they need them to perform a specific, necessary task and only for the minimal amount of time necessary. 

## Architecture Diagram ##
The following diagram depicts the application and infrastructure components on which this lab is built.

## Prerequisite Configuration ##
The following infrastructure components were configured and deployed to the Diversey Bank AWS environment to support this lab.

### Network ###

**VPC**: cicero-vpc (10.0.0.0/16)


| AZ         | Subnet Name  | CIDR Block    | Attached To |
|------------|--------------|---------------|-------------|
| us-east-1a | private-app-1a  | 10.0.0.0/24 | cicero-lambda |
| us-east-1a | private-db-1a | 10.0.2.0/24 | customer-account-db  |
| us-east-1b | private-app-1b  | 10.0.1.0/24 | cicero-lambda |
| us-east-1b | private-db-1b | 10.0.3.0/24 | customer-account-db |

### S3 Bucket ###
An S3 bucket called *diverseybank-internal-static-s3* contains one file called *cicero-spa.html*. This file is a single-page application (SPA) that contains the application logic needed to render the Cicero application within the employee's web browser.

### CloudFront Distribution ###
This distribution fronts web traffic to *cicero.diverseybank.com* and serves the requestor the SPA.html file from the S3 bucket.

<img width="1784" height="646" alt="distribution" src="https://github.com/user-attachments/assets/f353c0c7-96b5-4ea2-afec-c2c4d80c44ce" />

<img width="1788" height="272" alt="distribution-origin" src="https://github.com/user-attachments/assets/a6573837-5296-4847-8fbf-e1a1684dc647" />

### MySQL Database ###
A MySQL database called *customer-account-db* contains the customer account data needed to populate the Cicero application.

<img width="2540" height="190" alt="db" src="https://github.com/user-attachments/assets/fca169f5-6049-41f4-8e44-79d062194625" />

The following is a break-down of the database's tables.
| Table | Columns | Purpose |
|-------|---------|---------|
|abc    | abc     | abc     |

### KMS Key ###
A customer-managed symmetric key called *customer-account-pii-key* was created using Key Management Service and is used to both encrypt sensitive (PII) data elements prior to being stored in the database as well as decrypt those same elements prior to being rendered on the employee's screen within Cicero. A separate key (not pictured) is also used for volume-level encryption of the database, providing protection for all data at-rest.
<img width="1686" height="86" alt="kms key" src="https://github.com/user-attachments/assets/4f154d61-c105-4c47-9424-237cd0dc7fa5" />






## References ##
https://www.crowdstrike.com/en-us/cybersecurity-101/identity-security/just-in-time-access/
https://www.crowdstrike.com/en-us/cybersecurity-101/identity-security/principle-of-least-privilege-polp/
