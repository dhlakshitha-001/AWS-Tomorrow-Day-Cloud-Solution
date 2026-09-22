# Task 01 – Requirements Analysis

## 📌 Overview

This section documents the requirements analysis for the **Tomorrow Day AWS Cloud Solution** case study.

The purpose of this task is to identify the business requirements, functional requirements, and expected users before designing and implementing the AWS cloud infrastructure.

---

# 🎯 Case Study Summary

Tomorrow Day is a global news and business information platform serving millions of users worldwide.

The Asia audience consists of approximately **12.8 million users** and generates around **90 million page views per month**.

The existing infrastructure is hosted in a physical data center in China, where the current lease is approaching expiration.

To address the business and technical challenges, the proposed solution moves the required infrastructure to **Amazon Web Services (AWS)**.

---

# 1. Business Requirements

The proposed cloud solution should address the following business requirements.

### 1.1 Scalability

The infrastructure should be capable of supporting the existing user base and future traffic growth.

The architecture should allow computing and database resources to be expanded when required.

### 1.2 Reduced Latency for Asian Users

The solution should improve application responsiveness for users across Asia by hosting the infrastructure within an AWS region geographically suitable for the target audience.

### 1.3 Reduced Physical Data-Center Dependency

Moving infrastructure to AWS reduces dependency on maintaining physical servers and data-center facilities.

### 1.4 Reduced Maintenance Overhead

AWS managed services can reduce the amount of physical infrastructure maintenance required by the organization.

### 1.5 Faster Infrastructure Deployment

Infrastructure should be deployable more quickly than a traditional physical data-center environment.

Infrastructure as Code using **AWS CloudFormation** supports repeatable infrastructure deployment.

### 1.6 Improved Availability and Reliability

The cloud environment should provide a foundation that can be expanded to support higher availability and reliability.

### 1.7 Support for Growing Traffic

The infrastructure should be capable of supporting the organization's growing web traffic and increasing number of users.

### 1.8 Cost Efficiency

The cloud solution should reduce the need for large upfront physical infrastructure investments and provide flexible resource usage.

### 1.9 Secure Infrastructure Separation

Public-facing web resources should be separated from backend database resources.

The database should not be directly exposed to the public internet.

### 1.10 Manageable Cloud Infrastructure

The solution should provide centralized management of infrastructure resources through AWS services and Infrastructure as Code.

---

# 2. Functional Requirements

The following functional requirements were identified for the proposed solution.

| ID    | Functional Requirement                                                               |
| ----- | ------------------------------------------------------------------------------------ |
| FR-01 | The system shall provide a publicly accessible web server.                           |
| FR-02 | The web server shall support HTTP traffic.                                           |
| FR-03 | Administrative access to the web server shall be controlled through SSH rules.       |
| FR-04 | The solution shall provide a relational database.                                    |
| FR-05 | The web server shall be able to communicate with the database.                       |
| FR-06 | Database access shall use TCP port 3306 for MySQL communication.                     |
| FR-07 | The database shall not be directly accessible from the public internet.              |
| FR-08 | Public and private resources shall be separated using VPC subnets.                   |
| FR-09 | Security Groups shall control communication between application and database layers. |
| FR-10 | The database shall use persistent managed storage through Amazon RDS.                |
| FR-11 | AWS resources shall be manageable through the AWS Management Console.                |
| FR-12 | The infrastructure shall be deployable using AWS CloudFormation.                     |
| FR-13 | Infrastructure resources shall follow a consistent naming convention.                |
| FR-14 | Infrastructure resources should support future scalability and enhancement.          |

---

# 3. Expected Users

The proposed platform is expected to support different categories of users and infrastructure operators.

## 3.1 External Users

The main users are the organization's news and business information customers.

The case study identifies approximately:

**12.8 million users across Asia**

These users access the public-facing web application through the internet.

---

## 3.2 Internal IT / Cloud Operations Team

The internal infrastructure and cloud operations teams are responsible for:

* Managing AWS resources
* Monitoring infrastructure
* Managing network configuration
* Maintaining EC2 instances
* Managing database infrastructure
* Monitoring security
* Troubleshooting infrastructure issues

---

## 3.3 System Administrators

System administrators may require controlled administrative access to the EC2 web server for:

* Server configuration
* Software installation
* Troubleshooting
* Maintenance
* Monitoring

Administrative access is restricted through Security Group rules.

---

## 3.4 Developers

Developers may interact with the application and database layers during:

* Application development
* Testing
* Deployment
* Database integration
* Application troubleshooting

---

# 4. Non-Functional Considerations

Although the task primarily focuses on business and functional requirements, the following non-functional considerations were identified.

### Security

The database should remain private and only accept required traffic from the application layer.

### Performance

The infrastructure should provide suitable network performance for the target Asian audience.

### Scalability

The architecture should allow additional compute and database capacity to be introduced when required.

### Reliability

The architecture should provide a foundation that can be extended with additional availability mechanisms.

### Maintainability

Using AWS managed services and CloudFormation improves infrastructure management and repeatability.

### Cost Management

The infrastructure should avoid unnecessary resources and support efficient cloud resource usage.

---

# 5. Requirement-to-Solution Mapping

| Requirement                     | Proposed AWS Solution                                   |
| ------------------------------- | ------------------------------------------------------- |
| Scalability                     | EC2 + RDS architecture with future Auto Scaling support |
| Reduced latency                 | AWS region located in Asia                              |
| Reduced physical infrastructure | AWS cloud services                                      |
| Lower maintenance               | Managed AWS services                                    |
| Faster deployment               | AWS CloudFormation                                      |
| Improved security               | VPC + Security Groups + private database                |
| Public web access               | EC2 in public subnet                                    |
| Relational database             | Amazon RDS MySQL                                        |
| Secure DB access                | DB Security Group allowing port 3306 from Web SG        |
| Network separation              | Public and private subnets                              |
| Infrastructure consistency      | CloudFormation + resource naming convention             |
| Future expansion                | Cloud-based architecture                                |

---

# 6. Proposed Requirement Architecture

The requirements identified in this task lead to the following basic architecture:

```text
                    Internet Users
                          |
                          v
                 +------------------+
                 | Internet Gateway |
                 +--------+---------+
                          |
                          v
                 +------------------+
                 |  Public Subnet   |
                 |   10.0.1.0/24    |
                 |                  |
                 | EC2 Web Server   |
                 +--------+---------+
                          |
                     TCP 3306
                          |
                          v
                 +------------------+
                 | Private Subnets  |
                 |                  |
                 | RDS MySQL        |
                 | 10.0.2.0/24      |
                 | 10.0.3.0/24      |
                 +------------------+
```

---

# 7. Security Requirements

The following basic security requirements were identified:

1. The web server should be placed in a public subnet because it requires public web access.
2. The database should be placed in private subnets.
3. HTTP traffic should be allowed to the web server.
4. SSH access should be restricted to the administrator's source.
5. MySQL port `3306` should not be open to the internet.
6. Database access should be permitted only from the web-server Security Group.
7. RDS public access should be disabled.
8. AWS credentials, passwords, private keys, and other secrets should not be stored in the GitHub repository.

---

# 8. Expected Outcome

After completing the requirements analysis, the proposed AWS solution should provide:

* A public-facing web server
* A private relational database
* Secure application-to-database communication
* Network segmentation
* Controlled network access
* Cloud-based infrastructure
* Infrastructure as Code capability
* A foundation for future scalability and high availability

---

# 9. Task 01 Completion

**Task:** Requirements Analysis
**Status:** ✅ Completed

The business requirements, functional requirements, expected users, security considerations, and requirement-to-solution mapping have been identified and documented.

---

## 📂 Related Documentation

The complete project report is available at:

```text
documentation/AWS_Tomorrow_Day_Cloud_Solution_Report.pdf
```

The original assignment brief is available at:

```text
documentation/AWS_Tomorrow_Day_Assignment_Brief.pdf
```

The overall project README is available at:

```text
README.md
```

---

## 📁 Task 01 Directory

```text
screenshots/
└── task-01-requirements/
    └── README.md
```

This README documents **Task 01 – Requirements Analysis** for the Tomorrow Day AWS cloud infrastructure case study.
