# Draft-IAM-Customer-Servicing-Portal

## Table of Contents ##
- [Agent Experience Walkthrough](#agent-experience-walkthrough)
- [Prerequisite Configuration](#prerequisite-configuration)
- [RBAC](#role-based-access-control-rbac)
- [IAM Policy](#aws-iam-policies)
- [Ideal Enhancements](#ideal-enhancements)
- [Conclusion](#conclusion)

## Overview ##

Diversey Bank is a fictional financial institution headquartered in Chicago, Illinois. Diversey Bank offers a variety of consumer banking solutions to the Midwest region. This lab has built an internal customer-servicing application called Cicero, which is used by customer support agents to service customer accounts. The AWS Free Tier was leveraged for the deployment of services when available. Portions of the application, mainly the front-end, have been "vibe coded" to quickly mock the website so that more time can be allotted to the core intent. This lab will not deep-dive into the security configuration of the infrastructure components as that is out-of-scope. If that is desired, however, check out [AWS-Security-Build](https://github.com/BrandonVolker/AWS-Security-Build).

## Lab Goal ##
This lab seeks to demonstrate a basic competency of the Identity and Access Management (IAM) best-practices that may be implemented to securely grant user access to a web application that contains sensitive customer data, such as role-based access control (RBAC), the Principle of Least Privilege and Just-in-time (JIT) access. A successful outcome is one in which an agent can access only the data that is authorized for their role, that access is denied to agents outside that role, and that access is time-bound to the minimum duration necessary for the business use-case.

**RBAC** is a mechanism where you allow users to access certain resources based on permissions defined for the roles they are assigned to. 

**Principle of Least Privilege** is a computer security concept and practice that gives users limited access rights based on the tasks necessary to their job. 

**JIT** is a dynamic, on-demand approach to access control that grants human and non-human identities permissions to an application or system only when they need them to perform a specific, necessary task and only for the minimal amount of time necessary.

## Agent Experience Walkthrough ##

### Jim Hopper - A user with full data access ###
An agent browses to *cicero.diverseybank.com* on their company-issued laptop. The agent clicks *Sign In* to initiate the authentication process.
<img width="3440" height="1328" alt="cicero ui" src="https://github.com/user-attachments/assets/c86d5dda-362d-40b8-9fbd-0b68df66441b" />

The agent enters their company email address and clicks *Next*.
<img width="3440" height="1346" alt="hopper-sign-in" src="https://github.com/user-attachments/assets/9945775f-accf-4dbe-a93f-883c9c9fd86c" />

The agent is given a choice of logging in with their security key or password (which would prompt for a TOTP). The agent selects the security key option, enters their PIN, and touches their security key.
<img width="3440" height="1242" alt="image" src="https://github.com/user-attachments/assets/4def98e3-6602-4a8f-877e-5e472e1c8378" />

<img width="3440" height="1124" alt="plugin" src="https://github.com/user-attachments/assets/266f784a-6eaa-4a74-b621-63485f016500" />

<img width="3440" height="1202" alt="touch" src="https://github.com/user-attachments/assets/7d078ec8-78ad-403e-8d98-df0bea2804ff" />


The following is provided to demonstrate the password + TOTP alternative. The agent has previously registered with Google Authenticator.

<img width="3440" height="1332" alt="password" src="https://github.com/user-attachments/assets/0f3053d6-5459-4087-97eb-9cd92fd42111" />

<img width="3440" height="1360" alt="otp" src="https://github.com/user-attachments/assets/62460e10-a4f1-4fb6-876d-76adb34cb6f6" />



The agent has been authenticated to Cicero.

<img width="3440" height="1382" alt="portal landing" src="https://github.com/user-attachments/assets/02fbea30-35ec-4aea-b33e-84850e48044e" />

The agent interacts with the customer search feature which accepts customer ID, name, and phone number.
<img width="3438" height="1356" alt="partial search" src="https://github.com/user-attachments/assets/e45ceca1-87e0-4a5a-852a-03853124935b" />

The agent selects a customer which renders their profile on-screen.
<img width="1354" height="1388" alt="customer profile masked" src="https://github.com/user-attachments/assets/d210cced-33c1-4ba2-a597-e46bb0f9d5c0" />

The customer's DOB, Tax ID, and account number is masked. The agent clicks *View sensitive fields* with a valid business use-case.

The agent is required to fill out a brief form to justify their request. They enter a CALL ID # and a description.
<img width="1324" height="1378" alt="filled form" src="https://github.com/user-attachments/assets/58896e0c-5c24-42ae-8597-236325983f9f" />

After the request has been validated, the sensitive data is displayed on-screen for a duration of five minutes after which it disappears and requires a new request.
<img width="1326" height="1388" alt="unmasked" src="https://github.com/user-attachments/assets/69a45a86-0b62-45a1-b21c-4e859812d13a" />

### Max Mayfield - An agent with identity data access ###

The agent follows the same log-in process described above using their company-issued security key. The agent's access is restricted to identity data only. Notice how the account number (financial data) is not available.
<img width="1644" height="1384" alt="maxmayfield-unmasked" src="https://github.com/user-attachments/assets/4abd2da1-32d5-445b-9f53-d3884897071a" />

### Dustin Henderson - An agent with financial data access ###

The agent follows the same log-in process described above using their company-issued security key. The agent's access is restricted to financial data only. Notice how the customer's DOB and Tax ID (SSN/ITIN) are not available for viewing.
<img width="1630" height="1390" alt="dustin-unmasked" src="https://github.com/user-attachments/assets/0f1dd874-7f5e-4739-b450-b6b7b5d82110" />

### Mike Wheeler - An agent with no sensitive data access ###

The agent follows the same log-in process described above using their company-issued security key. The agent's access is restricted to general customer data. Notice how the customer's DOB and Tax ID (SSN/ITIN), and account number are not available for viewing.
<img width="1618" height="1366" alt="mikewheeler-noaccess" src="https://github.com/user-attachments/assets/4ee8eec1-3fbd-4778-8f3e-f68334b080fe" />

## Architecture Diagram ##
The following diagram depicts the application and infrastructure components on which this lab is built.
<img width="1336" height="1002" alt="arch diagram" src="https://github.com/user-attachments/assets/4a494d84-c551-41d2-9ec4-e108172dc8cd" />


## Prerequisite Configuration ##
The following infrastructure components were configured and deployed to the Diversey Bank AWS environment to support this lab.

### Network ###

**VPC**: cicero-vpc (10.0.0.0/16)


| AZ         | Subnet Name  | CIDR Block    | Attached To |
|------------|--------------|---------------|-------------|
| us-east-1a | private-app-1a  | 10.0.0.0/24 | cicero-search-lambda, cicero-restricted-lambda, cicero-privileged-lambda, kms-endpoint, sts-endpoint |
| us-east-1a | private-db-1a | 10.0.2.0/24 | customer-account-db  |
| us-east-1b | private-app-1b  | 10.0.1.0/24 | cicero-search-lambda, cicero-restricted-lambda, cicero-privileged-lambda, kms-endpoint, sts-endpoint |
| us-east-1b | private-db-1b | 10.0.3.0/24 | customer-account-db |

### CloudFront Distribution ###
This distribution fronts web traffic to *cicero.diverseybank.com* and serves the requestor content that is stored in an S3 bucket.

<img width="1632" height="592" alt="distribution" src="https://github.com/user-attachments/assets/b1e92f78-c6a1-43c6-a6c1-c485c0d08430" />

<img width="1634" height="496" alt="origin" src="https://github.com/user-attachments/assets/306a19e0-60d6-44ab-8db6-503bb600a34f" />



### S3 Bucket ###
The S3 bucket is called *diverseybank-internal-static-s3* and contains the following three files. These files represent the single-page application (SPA) that contains the application logic needed to render the Cicero application within the employee's web browser.

<img width="1636" height="434" alt="s3-objects" src="https://github.com/user-attachments/assets/6ba87638-71a2-4acf-9077-4121fa7a5ffb" />


| File Name | Description |
|------------|--------------|
| app.js | Contains the application's logic (signing in/out, calls to the API, rendering search results) |
| index.html | Contains the structural container of the app with static text/buttons  |
| styles.css | Contains the application's visuals (colors, fonts, spacing) |


Public access to the bucket is blocked and the bucket policy is allowing only connections from the CloudFront distribution.
<img width="1628" height="652" alt="bucket policy" src="https://github.com/user-attachments/assets/bf0ab860-24b1-43e4-bc36-00828d3b95bc" />


### MySQL Database ###
A MySQL database called *customer-account-db* contains the customer account data needed to populate the Cicero application. Note: All customer data used in this lab is simulated test data.

<img width="2354" height="86" alt="rds" src="https://github.com/user-attachments/assets/fad2a5bd-ddf3-4b22-8c08-bd64e5ace67e" />


Table **customer-info** contains the identity information related to the Bank's customers. Date of Birth and Tax ID are encrypted at the field-level as they are deemed sensitive data elements.
<img width="3104" height="968" alt="customer_info" src="https://github.com/user-attachments/assets/d2b0fa09-e073-4b90-a067-0b93956461c3" />


Table **account-info** contains the financial information held by the Bank's customers. Account number is encrypted at the field-level as it is deemed a sensitive data element.
<img width="1966" height="968" alt="account_info" src="https://github.com/user-attachments/assets/c727fbde-2ac2-4ff9-b1a3-56fdf28e988f" />


### KMS Key ###
A customer-managed symmetric key called *customer-account-pii-key* was created using Key Management Service (KMS) and was used to both encrypt sensitive data elements prior to being stored in the database as well as decrypt those same elements prior to being rendered on the employee's screen within Cicero. A separate key (not pictured) is also used for volume-level encryption of the database, providing protection for all data at-rest.

Two symmetric keys support the encryption/decryption of the sensitive data elements. 

*customer-identity-key* has encrypted customer Date of Birth and Tax ID prior to storing them within the MySQL database. This key is also used by the *cicero-privileged-lambda* function to decrypt these values prior to rendering them on-screen within Cicero.
*customer-financial-key* has encrypted customer account number prior to storing it within the MySQL database. This key is also used by the *cicero-privileged-lambda* function to decrypt these values prior to rendering them on-screen within Cicero.

<img width="1512" height="120" alt="kms keys" src="https://github.com/user-attachments/assets/7cb4cdcf-7174-45bb-8de0-eaacaa1f27bf" />

### Dynamo DB  ###
The following DynamoDB table is called *cicero-jit-grants-table* and is used to store each active, time-boxed access grant. The *cicero-privileged-lambda* function checks this table prior to decrypting sensitive data elements for the requesting agent. When the agent requests access to the sensitive data through Cicero, they must provide a valid support ticket number (and optional business justification) which is then validated against a system of record. Once validated, the grant is created in this table. Note: That system of record is out of scope for this lab but would be implemented in a production environment.

<img width="2892" height="118" alt="grants table" src="https://github.com/user-attachments/assets/e80739ca-b125-40cf-a218-2e8579b31300" />


The following is a look at the grants stored within the table after two agents have just requested access to view those sensitive fields. Grants have an assigned TTL of (5) minutes.
<img width="2988" height="300" alt="grants" src="https://github.com/user-attachments/assets/3b632c89-6519-4fd2-b0b7-5b009dfa7411" />


### Lambda Functions ###
The following four Lambda functions execute the back-end infrastructure calls (via Python scripts attached to the Functions) needed to render data within Cicero.

<img width="1600" height="388" alt="lambda functions" src="https://github.com/user-attachments/assets/d08e42c1-f517-4ff9-a305-f3e6c05ebb42" />


| Function Name | Description |
|------------|--------------|
| cicero-search-lambda |  Performs the customer look-up in MySQL DB |
| cicero-restricted-lambda | Returns a customer profile with non-sensitive data from MySQL DB |
| cicero-privileged-lambda | Validates the JIT grant then returns the same customer profile with sensitive data included  |
| cicero-grant-acccess-lambda | Creates a short-lived grant in DynamoDB, authorizing the browser to invoke unmasked sensitive data via a call to /full |


### Role-Based Access Control (RBAC) ###

#### Cognito ####

A User Pool called *cicero-user-pool* contains the four agent identities that are used in this lab.
<img width="2378" height="380" alt="user pool" src="https://github.com/user-attachments/assets/52cfaf0d-ae10-4420-8c47-1dc0bd91de6d" />

<img width="1782" height="364" alt="users" src="https://github.com/user-attachments/assets/591d4f57-0285-4447-8158-2c338c5708d6" />

<img width="2066" height="274" alt="groups" src="https://github.com/user-attachments/assets/bd3ca8fd-d851-4540-baad-95c73e11eac6" />


Agents have been assigned to the following groups.

| Agent Name| Group | Privilege |
|------------|--------------| ---|
| Mike Wheeler | cicero-no-pii-access | No sensitive data access |
| Max Mayfield | cicero-identity-access | Can view DOB and Tax ID (SSN/ITIN) |
| Dustin Henderson| cicero-financial-access  | Can view account number |
| Jim Hopper | cicero-full-access | Can view DOB, Tax ID (SSN/ITIN), account number |


All agents have been enrolled in passwordless passkey authentication. Password and Time-based One-Time Password (TOTP) via authenticator app is supported as a fall-back option. Note: Cognito requires the password authenticator to be enabled, otherwise, it would be disabled for this lab. The session will also implement a 15-minute idle timeout and an 8-hour absolute timeout, after which time, the agent will be required to re authenticate, regardless of activity .
<img width="1974" height="506" alt="sign-in methods" src="https://github.com/user-attachments/assets/ac0dbb4a-096b-4df7-b222-fbbbb5dfd7b3" />


#### JWT ####

After an agent authenticates, Cognito delivers a JSON Web Token (JWT) to the agent browser where it is stored throughout the duration of the session.

**Mike Wheeler JWT**
<img width="1328" height="784" alt="wheeler-jwt" src="https://github.com/user-attachments/assets/c7badf20-b084-4926-ac50-c215aeedaab1" />

**Max Mayfield JWT**
<img width="1324" height="790" alt="mayfield-jwt" src="https://github.com/user-attachments/assets/62b45144-fc48-48f0-977c-e2b20a8eba67" />

**Dustin Henderson JWT**
<img width="1322" height="790" alt="henderson-jwt" src="https://github.com/user-attachments/assets/9d31b306-3ff1-4e5d-8138-5b0a7fb2ab5e" />

**Jim Hopper JWT**
<img width="1330" height="784" alt="hopper-jwt" src="https://github.com/user-attachments/assets/6e6ef154-7a17-48dc-bc53-e1592c6168f0" />

### AWS IAM Policies ###
Seven (7) IAM roles have been created to support this lab. The following is a break-down of each.

| Role  | Description |
|------------|--------------|
| cicero-search-lambda-role | Execution role assumed by Lambda to search the MySQL DB based on the agent's search |
| cicero-restricted-lambda-role | Execution role assumed by Lambda to look-up a specific customer in the MySQL DB and return that profile (sensitive fields are masked) |
| cicero-grant-access-lambda-role | Execution role assumed by Lambda to write the JIT grant to DynamoDB once the agent's ticket has been validated (again, this validation is out-of-scope for the lab but that's the intent)  |
| cicero-privileged-lambda-role | Execution role assumed by Lambda to read the customer record (including encrypted ciphertext of the sensitive fields), check the DynamoDB table for an active grant and to assume one of the decrypt roles below.   |
| cicero-decrypt-full-role | Assumed by cicero-privileged-lambda based on the agent's assigned Cognito group to decrypt both the identity and financial data fields from the customer record |
| cicero-decrypt-identity-role | Assumed by cicero-privileged-lambda based on the agent's assigned Cognito group to decrypt the identity data fields (DOB, Tax ID (SSN/ITIN) from the customer record|
| cicero-decrypt-financial-role | Assumed by cicero-privileged-lambda based on the agent's assigned Cognito group to decrypt the financial data fields (account number) from the customer record |

### Code Snippet of Decrypt Logic ###
Below are important snippets of Python code attached to the *cicero-privileged-lambda* function.

Lambda reads the agent's Cognito user group from the JWT token and attempts a translation to the ARN of the IAM role that it assumes to call KMS and decrypt the sensitive data fields. Notice from the comment, if the agent belongs to the cicero-no-pii-access group, no role is assumed and the agent sees sensitive dats fields as RESTRICTED within Cicero.

<img width="1120" height="222" alt="mapping" src="https://github.com/user-attachments/assets/5dee967c-82fc-47e1-9c61-1ad4d2aebaaf" />

The following is a try_decrypt() is a helper function that attempts to decrypt one field's value. It requires the KMS client (scoped into the assumed role), the encrypted value (as read from MySQL DB), and the KMS key. If an active JIT is not found in the DynamoDB table, no KMS client will have been created and decryption is skipped and the function returns RESTRICTED to the agent.
<img width="1264" height="108" alt="encrypted code snippet" src="https://github.com/user-attachments/assets/f5bec849-9f04-4b7e-ad17-1c5d05145d51" />

The following is the invocation of the function above, once per sensitive data field. Notice the two different keys used depending on the data fields being decrypted.
<img width="1616" height="480" alt="lambda code snippet" src="https://github.com/user-attachments/assets/ab848986-b8fd-41ab-a870-dcbd2aa9108c" />

### IAM Policy ###
The following IAM policies are attached to the respective IAM roles (assumed by the Lambda functions) and represents the enforcement point for the decrypt operation.


**cicero-decrypt-full-role**
<img width="776" height="390" alt="cicero-decrypt-full-rolePolicy" src="https://github.com/user-attachments/assets/5a4cf143-01a8-4017-b3b8-9a4f9c9df482" />

**cicero-decrypt-identity-role**
<img width="826" height="318" alt="cicero-decrypt-identity-rolePolicy" src="https://github.com/user-attachments/assets/17709fb8-a2c9-4b60-86e9-6cf439c920df" />

**cicero-decrypt-financial-role**
<img width="836" height="320" alt="cicero-decrypt-financial-rolePolicy" src="https://github.com/user-attachments/assets/3ed0d2c3-23f6-463a-86b6-830bb5682335" />

The following IAM policies are attached to the respective IAM execution roles used by Lambda.


**cicero-search-lambda-role**

<img width="962" height="628" alt="cicero-search-lambda-policy" src="https://github.com/user-attachments/assets/659ef1fc-694e-4c06-93fb-4ce774d68afd" />


**cicero-restricted-lambda-role**

<img width="952" height="622" alt="cicero-restricted-lambda-policy" src="https://github.com/user-attachments/assets/1f20ede4-bfeb-4b30-a377-dafa6cf30f5c" />


**cicero-grant-access-lambda-role**

<img width="954" height="470" alt="cicero-grant-access-lambda-policy" src="https://github.com/user-attachments/assets/40c7096b-4c41-46e4-a394-764625c2f12b" />


**cicero-privileged-lambda-role**

<img width="990" height="671" alt="cicero-privileged-lambda-policy-1" src="https://github.com/user-attachments/assets/ed44bee1-f2db-4fec-8821-d6a9b170ae60" />
<img width="952" height="308" alt="cicero-privileged-lambda-policy-2" src="https://github.com/user-attachments/assets/53a1ba72-81da-437d-9623-1389250d09ec" />


### API Gateway ###
The following API called *cicero-portal-api* is configured with four routes, each mapped to a Lambda function. Each route is protected by an authorizer that validates the Cognito-issued JWT included with each API request (from the agent browser) before the request reaches the Lambda functions.
<img width="1954" height="522" alt="api routes" src="https://github.com/user-attachments/assets/6e0dedf2-ed29-4629-afa1-2a302cac8e6a" />

| Method | Path | Mapped to Lambda |
|------------|--------------| ---|
| GET | /customers/{id} | cicero-restricted-lambda |
| GET | /customers/{id}/full | cicero-privileged-lambda |
| POST | /customers/{id}/request-access  | cicero-grant-access-lambda |
| GET | /customers/search | cicero-search-lambda |

### Logging ###
The following Python code snippet on *cicero-privileged-lambda* is responsible for writing unmask events to CloudWatch logs.
<img width="1260" height="312" alt="logging snippet" src="https://github.com/user-attachments/assets/ab09aeed-1a35-4fb1-85ab-ff40846654e6" />


The timestamp, agent UUID, customer ID, agent user group, the ticket ID the agent supplied in the form, and the fields revealed to the agent are visible.
<img width="1347" height="766" alt="cloudwatch" src="https://github.com/user-attachments/assets/7e63c138-6764-4424-81d6-ce003cc94567" />

### Ideal Enhancements ###
The following are recognized as ideal enhancements to increase the overall security posture of Cicero, but are considered out-of-scope for this IAM lab.

**Validation of agent-submitted ticket** - The key to JIT access is sufficient validation of the agent-submitted ticket (e.g. CALL# ticket is validated against the system of record for active phone calls).
**Suspicious activity monitoring** - All user sessions would be monitored for anomalous activity such as multiple requests for sensitive information within a certain timeframe (e.g. 6 requests within a 10 minute period would trigger an alert and/or account lockout).

## Conclusion ##
This lab has implemented a few critical IAM access control best-practices such as **role-based access control (RBAC)**, **the principle of least privilege**, and **just-in-time (JIT) access**. Agents are assigned to specific Cognito user groups corresponding to their role and each group's permissions are scoped such that agents can view only the customer information required for their role for a limited time duration after they have provided proper justification. These controls work together to reduce the blast radius of an account takeover attack.


## References ##
https://www.crowdstrike.com/en-us/cybersecurity-101/identity-security/just-in-time-access/
https://www.crowdstrike.com/en-us/cybersecurity-101/identity-security/principle-of-least-privilege-polp/
