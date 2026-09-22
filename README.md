# AWS Cloud Solution for Tomorrow Day

> **Academic AWS Cloud Infrastructure & Infrastructure-as-Code Project**

![AWS](https://img.shields.io/badge/AWS-Cloud-orange)
![Amazon VPC](https://img.shields.io/badge/Amazon-VPC-blue)
![Amazon EC2](https://img.shields.io/badge/Amazon-EC2-orange)
![Amazon RDS](https://img.shields.io/badge/Amazon-RDS-blue)
![CloudFormation](https://img.shields.io/badge/AWS-CloudFormation-orange)
![Status](https://img.shields.io/badge/Status-Completed-success)

---

## 📌 Project Overview

This project presents an **AWS cloud infrastructure solution for the Tomorrow Day case study**, a global news and business information platform serving a large audience across Asia.

The solution was designed to address the limitations of a traditional physical data-center environment by migrating the required infrastructure to AWS.

The implementation focuses on:

* Cloud networking
* Public and private subnet architecture
* Web server deployment
* Managed relational database
* Network security
* Secure application-to-database communication
* Infrastructure as Code using AWS CloudFormation
* Infrastructure testing and validation

> **Note:** This is an academic case-study implementation. The infrastructure is not an actual production environment of Tomorrow Day or News Corp.

---

## 🎯 Case Study

Tomorrow Day is a global provider of news and business information services with millions of users worldwide.

The Asia-Pacific audience represents approximately **12.8 million users**, generating around **90 million page views per month**.

The company currently operates infrastructure from a physical data center in China, but the existing data-center lease is approaching expiration.

The proposed AWS solution aims to provide:

* Lower latency for Asian users
* Reduced dependence on physical data-center infrastructure
* Lower infrastructure maintenance overhead
* Faster infrastructure deployment
* Improved scalability
* Better availability and reliability
* Secure separation between web and database layers
* Cost-efficient cloud infrastructure

---

# 🏗️ Architecture

## High-Level Architecture

```text
                         Internet Users
                               |
                               |
                        Internet Gateway
                               |
                               v
                  +-------------------------+
                  |       AWS VPC           |
                  |      10.0.0.0/16        |
                  |                         |
                  |  Public Subnet          |
                  |  10.0.1.0/24            |
                  |                         |
                  |  +-------------------+  |
                  |  | Amazon EC2         |  |
                  |  | Apache Web Server  |  |
                  |  +-------------------+  |
                  |          |              |
                  |          | TCP 3306     |
                  |          v              |
                  |  Private Subnet         |
                  |  10.0.2.0/24            |
                  |          |              |
                  |          v              |
                  |  +-------------------+  |
                  |  | Amazon RDS          |  |
                  |  | MySQL Database      |  |
                  |  +-------------------+  |
                  |                         |
                  |  Private Subnet 2       |
                  |  10.0.3.0/24            |
                  |                         |
                  +-------------------------+
```

### Traffic Flow

```text
Internet
   |
   v
Internet Gateway
   |
   v
Public Subnet
   |
   v
EC2 Web Server
   |
   | TCP 3306
   v
Private RDS MySQL
```

The database does **not** receive direct internet traffic.

---

# ☁️ AWS Services Used

| AWS Service      | Purpose                                     |
| ---------------- | ------------------------------------------- |
| Amazon VPC       | Isolated cloud network                      |
| Internet Gateway | Internet connectivity for the public subnet |
| Amazon EC2       | Hosts the web server                        |
| Amazon RDS       | Managed MySQL database                      |
| Security Groups  | Network-level access control                |
| Route Table      | Controls subnet traffic routing             |
| CloudFormation   | Infrastructure as Code                      |
| DB Subnet Group  | Places RDS within private subnets           |

---

# 🌐 Network Architecture

## VPC

**VPC Name:** `IT20263980_VPC`

**CIDR:** `10.0.0.0/16`

The VPC provides an isolated networking environment for the Tomorrow Day application.

### Subnets

| Subnet           | CIDR          | Type    | Purpose          |
| ---------------- | ------------- | ------- | ---------------- |
| Public Subnet    | `10.0.1.0/24` | Public  | EC2 Web Server   |
| Private Subnet   | `10.0.2.0/24` | Private | RDS              |
| Private Subnet 2 | `10.0.3.0/24` | Private | RDS subnet group |

---

## Internet Gateway

**Name:** `IT20263980_IGW`

The Internet Gateway provides internet connectivity to resources located in the public subnet.

Only the web-server layer is exposed through the public network path.

---

## Route Table

**Name:** `IT20263980_PublicRT`

The public route table contains:

```text
Destination: 0.0.0.0/0
Target: Internet Gateway
```

This allows internet-bound traffic from the public subnet.

The private database subnets do not use this public route for inbound database access.

---

# 🔐 Security Architecture

Security was implemented using separate Security Groups for the web and database layers.

## Web Server Security Group

**Name:** `IT20263980_WebSG`

### Inbound Rules

| Protocol | Port | Source      | Purpose               |
| -------- | ---: | ----------- | --------------------- |
| HTTP     |   80 | `0.0.0.0/0` | Public web access     |
| SSH      |   22 | My IP       | Administrative access |

### Outbound

The web server allows required outbound communication.

---

## Database Security Group

**Name:** `IT20263980_DB-SG`

### Inbound Rule

| Protocol | Port | Source                    | Purpose                               |
| -------- | ---: | ------------------------- | ------------------------------------- |
| MySQL    | 3306 | Web Server Security Group | Application-to-database communication |

The database Security Group does **not** allow MySQL access from the public internet.

### Security Design

```text
Internet
   |
   | HTTP :80
   v
[ Web Security Group ]
   |
   v
[ EC2 Web Server ]
   |
   | MySQL :3306
   | Source = Web Security Group
   v
[ DB Security Group ]
   |
   v
[ RDS MySQL ]
```

This implements a basic **two-tier security architecture**.

---

# 💻 Amazon EC2 Web Server

## Configuration

| Property         | Value                  |
| ---------------- | ---------------------- |
| Resource Name    | `IT20263980_WebServer` |
| Operating System | Amazon Linux 2023      |
| Service          | Apache HTTP Server     |
| Network          | Public Subnet          |
| Internet Access  | Enabled                |
| Web Port         | 80                     |
| SSH Port         | 22                     |

The EC2 instance hosts a simple Apache web page representing the Tomorrow Day application layer.

### Web Page

The deployed page displays:

```text
Tomorrow Day - AWS Cloud Solution
Student ID: IT20263980
Web Server: Amazon EC2
```

---

# 🗄️ Amazon RDS MySQL

## Configuration

| Property        | Value                      |
| --------------- | -------------------------- |
| Resource Name   | `IT20263980_Database`      |
| Engine          | MySQL                      |
| Network         | Private Subnets            |
| Public Access   | No                         |
| Database Port   | 3306                       |
| DB Subnet Group | `IT20263980_DBSubnetGroup` |
| Security Group  | `IT20263980_DB-SG`         |

The RDS database is deployed inside private subnets.

### Database Security

The RDS instance:

* Does not have public access
* Is not directly reachable from the internet
* Accepts MySQL traffic only from the web-server Security Group
* Uses a DB subnet group containing private subnets

---

# 🧩 Infrastructure as Code

## AWS CloudFormation

The infrastructure was also implemented using **AWS CloudFormation**.

CloudFormation enables the AWS resources to be defined as code instead of manually creating each resource through the AWS Management Console.

### Template

```text
cloudformation/
└── tomorrow-day-infrastructure.yaml
```

The CloudFormation template defines resources including:

* VPC
* Internet Gateway
* Public Subnet
* Private Subnets
* Route Table
* Route Table Association
* Web Security Group
* Database Security Group
* EC2 Web Server
* RDS MySQL
* RDS DB Subnet Group

---

## CloudFormation Stack

**Stack Name:**

```text
IT20263980_CloudFormation
```

The stack was successfully deployed and verified through the AWS CloudFormation console.

---

# 🏷️ Resource Naming Convention

A consistent resource naming convention was used throughout the project.

| Resource             | Name                         |
| -------------------- | ---------------------------- |
| VPC                  | `IT20263980_VPC`             |
| Public Subnet        | `IT20263980_PublicSubnet`    |
| Private Subnet       | `IT20263980_PrivateSubnet`   |
| Private Subnet 2     | `IT20263980_PrivateSubnet_2` |
| Internet Gateway     | `IT20263980_IGW`             |
| Route Table          | `IT20263980_PublicRT`        |
| Web Security Group   | `IT20263980_WebSG`           |
| DB Security Group    | `IT20263980_DB-SG`           |
| EC2                  | `IT20263980_WebServer`       |
| RDS                  | `IT20263980_Database`        |
| DB Subnet Group      | `IT20263980_DBSubnetGroup`   |
| CloudFormation Stack | `IT20263980_CloudFormation`  |

### Academic Identifier Note

`IT20263980` is a **fictional/placeholder academic identifier created for this case-study implementation**.

It is used only to maintain consistent naming across AWS resources and documentation.

It does **not** represent an actual student identification number.

---

# 🏷️ Resource Tags

The following tags were used where applicable:

| Key         | Value                |
| ----------- | -------------------- |
| StudentID   | `IT20263980`         |
| Project     | `TomorrowDay`        |
| Environment | `Assignment`         |
| Owner       | `D.Heshan.Lakshitha` |

The `StudentID` tag uses the same fictional identifier for consistency with the resource naming convention.

---

# 🧪 Testing & Validation

The deployed infrastructure was tested to verify the major functional and security requirements.

## 1. Web Server Accessibility

The EC2 web server was accessed through its public IP address.

### Expected Result

```text
Tomorrow Day - AWS Cloud Solution
Student ID: IT20263980
Web Server: Amazon EC2
```

**Status:** ✅ Passed

---

## 2. EC2 to RDS Connectivity

Connectivity from the EC2 web server to the RDS MySQL database was tested using the MySQL client.

Example:

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```

The connection validates that:

```text
EC2 Web Server
      |
      | TCP 3306
      v
RDS MySQL
```

is permitted by the database Security Group.

**Status:** ✅ Passed

---

## 3. RDS Public Access

The RDS configuration was inspected to verify that:

```text
Public Access = No
```

This confirms that the database is not directly exposed to the public internet.

**Status:** ✅ Passed

---

## 4. Security Group Validation

The database Security Group was verified to allow:

```text
MySQL
Port: 3306
Source: Web Server Security Group
```

The web Security Group was verified for:

```text
HTTP
Port: 80
Source: 0.0.0.0/0
```

and administrative SSH access from the configured administrator source.

**Status:** ✅ Passed

---

## 5. Subnet Validation

The EC2 instance was verified to be located in:

```text
Public Subnet
10.0.1.0/24
```

The RDS instance was verified to use:

```text
Private Subnet
10.0.2.0/24

Private Subnet 2
10.0.3.0/24
```

**Status:** ✅ Passed

---

# 📸 Screenshots

Screenshots documenting the implementation and testing are organized under the `screenshots/` directory.

## Task 3 – AWS Implementation

```text
screenshots/task-03-implementation/
```

Includes evidence for:

* VPC configuration
* Internet Gateway
* Security Groups
* EC2 configuration
* Web server browser test
* RDS configuration
* Public/private subnet configuration
* Database security configuration

## Task 4 – CloudFormation

```text
screenshots/task-04-cloudformation/
```

Includes evidence for:

* CloudFormation stack creation
* Parameters
* CREATE_COMPLETE status
* Stack resources
* Outputs
* CloudFormation EC2
* CloudFormation web-server test
* CloudFormation RDS
* Database Security Group
* EC2-to-RDS connectivity

## Task 5 – Testing

```text
screenshots/task-05-testing/
```

Includes evidence for:

* RDS public access validation
* Database Security Group rules
* Web Security Group rules
* EC2 public subnet verification
* RDS private subnet verification

---

# 📁 Repository Structure

```text
AWS-Tomorrow-Day-Cloud-Solution/
│
├── README.md
│
├── architecture/
│   ├── tomorrow-day-architecture.drawio
│   └── tomorrow-day-architecture.png
│
├── cloudformation/
│   └── tomorrow-day-infrastructure.yaml
│
├── documentation/
│   └── AWS_Cloud_Solution_Report.pdf
│
├── screenshots/
│   │
│   ├── task-01-requirements/
│   │   └── README.md
│   │
│   ├── task-02-architecture/
│   │   └── architecture-diagram.png
│   │
│   ├── task-03-implementation/
│   │   ├── 01_VPC_Details.png
│   │   ├── 01_VPC_Details_01.png
│   │   ├── 01_VPC_Details_02.png
│   │   ├── 02_Internet_Gateway.png
│   │   ├── 03_Web_Security_Group.png
│   │   ├── 04_Web_Server_Browser.png
│   │   ├── 05_EC2_Details.png
│   │   ├── 06_RDS_Details.png
│   │   ├── 07_RDS_Security_Group.png
│   │   ├── 08_EC2_Public_Subnet.png
│   │   ├── 09_Private_Subnet_2.png
│   │   ├── 09_RDS_Private_Subnet.png
│   │   ├── 10_DB_Security_Group.png
│   │   └── Webserver_creation.png
│   │
│   ├── task-04-cloudformation/
│   │   ├── 01_CloudFormation_Create_Stack.png
│   │   ├── 02_CloudFormation_Parameters.png
│   │   ├── 03_CloudFormation_CREATE_COMPLETE.png
│   │   ├── 04_CloudFormation_Stack_Create_Complete.png
│   │   ├── 05_CloudFormation_Resources.png
│   │   ├── 06_CloudFormation_Outputs.png
│   │   ├── 07_CF_EC2_Details.png
│   │   ├── 08_CF_WebServer_Test.png
│   │   ├── 09_CF_RDS_Details.png
│   │   ├── 10_CF_DB_SecurityGroup.png
│   │   └── 11_CF_EC2_RDS_Connectivity.png
│   │
│   └── task-05-testing/
│       ├── 17_RDS_No_Public_Access.png
│       ├── 18_DB_Security_Group_Rules.png
│       ├── 19_Web_Security_Group_Rules.png
│       ├── 20_EC2_Public_Subnet_Verification.png
│       └── 21_RDS_Private_Subnet_Verification.png
│
└── .gitignore
```

---

# 📚 Assignment Mapping

| Assignment Task               | Repository Location                                       |
| ----------------------------- | --------------------------------------------------------- |
| Task 1 – Requirements         | `README.md` + `screenshots/task-01-requirements/`         |
| Task 2 – Architecture         | `architecture/` + `README.md`                             |
| Task 3 – AWS Implementation   | `screenshots/task-03-implementation/`                     |
| Task 4 – CloudFormation       | `cloudformation/` + `screenshots/task-04-cloudformation/` |
| Task 5 – Testing & Validation | `screenshots/task-05-testing/`                            |

---

# 🔐 Security & GitHub Safety

The following sensitive information must **not** be committed to GitHub:

```text
AWS Access Keys
AWS Secret Access Keys
.pem files
Private SSH keys
RDS passwords
API keys
Access tokens
Credentials
Secrets
```

The CloudFormation template should use parameters or references instead of storing sensitive credentials directly in the repository.

A `.gitignore` file should be used to prevent accidental uploads of sensitive files.

---

# 💰 Cost Considerations

The solution was designed as an academic implementation using small AWS resources.

Cost considerations include:

* EC2 instance usage
* RDS instance usage
* EBS storage
* Data transfer
* Public IPv4 usage
* Other AWS service charges

Resources should be stopped or deleted after testing when they are no longer required to avoid unnecessary AWS charges.

---

# 🚀 Future Improvements

For a production-oriented architecture, the following improvements could be considered:

### High Availability

* Application Load Balancer
* Multiple EC2 instances
* Auto Scaling Groups
* Multi-AZ RDS deployment

### Performance

* Amazon CloudFront
* Amazon ElastiCache
* AWS Global infrastructure optimization

### Security

* AWS WAF
* AWS IAM least-privilege policies
* AWS Secrets Manager
* AWS Systems Manager
* CloudTrail

### Monitoring

* Amazon CloudWatch
* CloudWatch Alarms
* AWS CloudTrail
* AWS Backup

### DevOps

* GitHub Actions
* AWS CodePipeline
* Automated CloudFormation deployment
* CI/CD pipeline
* Infrastructure testing

### Scalability

```text
Users
  |
CloudFront
  |
Load Balancer
  |
Auto Scaling EC2
  |
RDS Multi-AZ
```

These improvements would make the architecture more suitable for a large-scale production environment.

---

# 🧠 What I Learned

Through this project, I gained practical experience in:

* Designing AWS VPC architectures
* Creating public and private subnets
* Configuring Internet Gateways
* Working with AWS route tables
* Deploying EC2 instances
* Configuring Apache web servers
* Deploying Amazon RDS MySQL
* Creating and configuring Security Groups
* Securing database access using Security Group references
* Understanding public vs private AWS resources
* Testing EC2-to-RDS connectivity
* Using AWS CloudFormation for Infrastructure as Code
* Organizing cloud infrastructure using naming conventions and tags
* Validating AWS infrastructure through practical testing
* Documenting cloud infrastructure for technical and academic purposes

---

# 📊 Project Outcome

The completed implementation demonstrates a basic AWS two-tier cloud architecture:

```text
                 ┌──────────────────────┐
                 │      Internet        │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ Internet Gateway     │
                 └──────────┬───────────┘
                            │
                            ▼
                 ┌──────────────────────┐
                 │ Public Subnet        │
                 │ 10.0.1.0/24          │
                 │                      │
                 │ EC2 + Apache         │
                 └──────────┬───────────┘
                            │
                       TCP 3306
                            │
                            ▼
                 ┌──────────────────────┐
                 │ Private Subnets      │
                 │                      │
                 │ RDS MySQL            │
                 │ 10.0.2.0/24          │
                 │ 10.0.3.0/24          │
                 └──────────────────────┘
```

The solution successfully demonstrates:

* AWS networking
* Compute deployment
* Managed database deployment
* Network security
* Public/private resource separation
* Infrastructure as Code
* Infrastructure testing and validation

---

# 📌 Project Status

**Status: Completed ✅**

The AWS infrastructure, CloudFormation implementation, testing, screenshots, and documentation have been completed for the academic case study.

---

# 👨‍💻 Author

**D. Heshan Lakshitha**

Cloud / DevOps Enthusiast
Sri Lanka

### Areas of Interest

* Cloud Computing
* DevOps
* Infrastructure as Code
* Kubernetes
* Linux
* Networking
* Cybersecurity
* Cloud Automation

---

# ⚠️ Disclaimer

This repository was created for **academic and portfolio purposes** based on the Tomorrow Day case study.

The architecture and infrastructure presented here are a student implementation and should not be interpreted as the actual infrastructure, architecture, configuration, or deployment of Tomorrow Day or News Corp.

The identifier `IT20263980` is a **fictional/placeholder identifier** used for academic resource naming and documentation consistency.

---

## 📄 Documentation

The detailed academic report is available at:

```text
documentation/AWS_Cloud_Solution_Report.pdf
```

The Infrastructure-as-Code template is available at:

```text
cloudformation/tomorrow-day-infrastructure.yaml
```

---

**AWS Cloud Infrastructure • Infrastructure as Code • Cloud Security • DevOps**
