# Serverless Architecture: Automated Daily Sales Reporting for AWS Café

## Project Scenario
The business requirement was to automate the generation of daily sales reports for the café without impacting the performance of the production web server. The previous solution relied on a resource-intensive cron job running on an EC2 instance. This project implements a fully serverless, event-driven architecture that decouples reporting tasks from the production environment to optimize performance and reduce operational costs.

## Project Objectives
- Deploy Lambda functions within a VPC to securely access an Amazon RDS database.
- Orchestrate a multi-function workflow to extract data and generate formatted reports.
- Implement an automated notification system using Amazon SNS.
- Establish a recurring schedule for the reporting process using Amazon EventBridge.

---

## Architecture Design
The following diagram illustrates the final serverless workflow, moving from a monolithic cron-based approach to a decoupled cloud-native design.

![Final Architecture](./Implementing%20a%20Serverless%20Architecture%20for%20the%20Café/Screenshots/architecture%20at%20the%20end.png)

---

## Technical Implementation Details

### 1. Networking and Security Configuration
To ensure secure communication, the data extraction logic was placed within the Lab VPC. 
- **LambdaSG:** A dedicated security group was created for the Lambda function to manage outbound traffic.
- **DatabaseSG:** The RDS security group was updated with an inbound rule to allow MySQL/Aurora traffic (Port 3306) specifically originating from the LambdaSG.

![Lambda Security Group](./Implementing%20a%20Serverless%20Architecture%20for%20the%20Café/Screenshots/LambdaSG.png)
![Database Security Group Configuration](./Implementing%20a%20Serverless%20Architecture%20for%20the%20Café/Screenshots/DB-SG.png)

### 2. AWS Lambda Functions
Two Python-based functions were utilized to handle the process:
- **salesAnalysisReportDataExtractor:** Configured with VPC access to private subnets to retrieve sales data from the database.
- **salesAnalysisReport:** Responsible for processing the extracted data and interacting with the notification service.

### 3. Notification System (Amazon SNS)
A standard SNS topic named `SalesReportTopic` was created. The reporting Lambda function uses this topic's ARN as an environment variable to publish the final report. An email subscription was established and verified to receive the automated alerts.

![SNS Topic](./Implementing%20a%20Serverless%20Architecture%20for%20the%20Café/Screenshots/SalesReportTopic.png)
![Email Subscription](./Implementing%20a%20Serverless%20Architecture%20for%20the%20Café/Screenshots/email.png)

### 4. Automated Scheduling (EventBridge)
To eliminate manual intervention, an Amazon EventBridge rule was configured. This rule uses a cron expression to trigger the salesAnalysisReport function at a specific time daily.

![EventBridge Rule](./Implementing%20a%20Serverless%20Architecture%20for%20the%20Café/Screenshots/EventBridge.png)

---

## Testing and Results
Validation was performed by manually triggering the reporting function. The logs confirmed successful connectivity to the RDS instance, and the sales report was successfully delivered to the configured email address.

![Execution Success](./Implementing%20a%20Serverless%20Architecture%20for%20the%20Café/Screenshots/Executing%20function:%20succeeded.png)

---

## Technical Stack
- **Compute:** AWS Lambda (Python 3.11)
- **Database:** Amazon RDS (MySQL)
- **Notifications:** Amazon SNS
- **Automation:** Amazon EventBridge
- **Networking:** Amazon VPC (Private Subnets, Security Groups)

---
**Documentation by Mina | Cloud & DevOps Project**
