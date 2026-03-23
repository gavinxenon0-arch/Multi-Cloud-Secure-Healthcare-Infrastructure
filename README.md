# Multi-Cloud Secure Healthcare Infrastructure

A hybrid **AWS + GCP healthcare-oriented infrastructure project** built for secure connectivity, controlled internal access, high availability, observability, and compliance-friendly logging.

## Overview

**Multi-Cloud Secure Healthcare Infrastructure** is a project that demonstrates how a security-focused application environment can be deployed across **AWS** and **GCP** while keeping sensitive backend systems protected and tightly controlled.

The platform uses:

- **Stateless compute in AWS São Paulo**
- **Stateless compute in GCP Iowa**
- **A centralized database hosted in Japan**
- **VPN connectivity between AWS and GCP**
- **An AWS Transit Gateway** to route traffic securely between environments

This architecture reflects the kind of design principles needed for **healthcare and other sensitive workloads**, where internal service protection, encrypted traffic, observability, incident response, and controlled access matter from day one.

## Project Goal

The goal of this project is to model a **secure, resilient, and observable multi-cloud infrastructure** that could support healthcare-style workloads where data protection, service reliability, and auditability are critical.

This project focuses on:

- secure multi-cloud networking
- private internal communication
- restricted service exposure
- centralized secrets management
- monitoring and alerting
- compliance-friendly log retention
- infrastructure as code through Terraform

## Architecture Summary

At a high level, this infrastructure includes:

- **AWS and GCP connected through VPN**
- **AWS Transit Gateway** connecting network paths to the VPC hosting the database in Japan
- **Stateless application compute** in AWS São Paulo and GCP Iowa
- **Load-balanced application delivery**
- **Auto Scaling protection for AWS EC2**
- **Golden AMIs** for standardized provisioning
- **CloudFront + Route 53** for secure ingress and caching
- **AWS WAF** for edge protection
- **Secrets Manager and Parameter Store** for secure configuration handling
- **CloudWatch, SNS, and Lambda** for monitoring, alerting, and incident response support
- **S3 and Kinesis Data Firehose** for logging, retention, and downstream SIEM integration

## Healthcare Security Design Focus

This project is framed around infrastructure practices that are highly relevant in healthcare environments:

- limiting unnecessary external exposure
- protecting sensitive backend resources
- encrypting traffic across layers
- separating stateless application services from centralized data systems
- maintaining audit trails
- preparing logs for security analysis
- improving operational response through monitoring and automation

This repository does **not yet claim formal healthcare compliance certification**, but it is designed around security and operational patterns that align well with **healthcare-grade infrastructure expectations**.

## Core Architecture

### Multi-Cloud Connectivity
AWS and GCP are connected through a **VPN**, allowing cloud environments to communicate securely.

An **AWS Transit Gateway** is used as the routing layer that connects traffic toward the VPC hosting the database in **Japan**.

The VPN-related design was also built to be easier to expand later by making configuration easier to manage through Terraform state references and related configuration structures.

### Stateless Application Layer
The application compute layer is intentionally **stateless**, which improves resilience and scalability.

Compute is deployed in:

- **AWS São Paulo**
- **GCP Iowa**

In AWS, EC2 instances are placed behind:

- an **Auto Scaling Group**
- a **Load Balancer**

To make deployment more consistent, the instances are launched from **Golden AMIs** that already contain the required packages and baseline configuration.

### Centralized Database Layer
The database is hosted in **Japan**, separated from the stateless compute layer.

This separation helps reflect a more controlled architecture where application services can scale independently while access to the data layer remains more tightly managed.

## Security Controls

### Internal-Only Service Exposure
Services that are intended to remain private are configured to use **endpoints and internal access patterns**, reducing unnecessary public exposure.

This supports a design more appropriate for **sensitive environments**, where not every component should be directly reachable from the internet.

### Secure Secrets and Configuration Handling
Sensitive application and database secrets are stored in **AWS Secrets Manager**.

Non-secret configuration values, such as selected database details, are stored in **AWS Systems Manager Parameter Store**.

The EC2 application layer accesses these values through an **Instance Profile / IAM Role**, allowing systems to retrieve what they need without hardcoding secrets into code or infrastructure.

Secrets are also **replicated across regions**, which improves resilience and reduces dependency on a single regional secret source.

### Restricted Origin Access
**CloudFront** is used as the public edge layer for traffic handling.

To protect the backend:

- the **ALB is restricted** so it should only receive traffic from CloudFront
- the request must also include a **secret header**
- that header value is stored in **Secrets Manager**

This adds another control layer between the public edge and the application backend.

### HTTPS and Certificate Management
HTTPS is used throughout the architecture.

The environment uses:

- **Route 53**
- **ACM certificates**
- a **hosted zone managed through Terraform**
- an imported hosted zone structure

CloudFront and ALB communication is also configured for HTTPS using the certificate setup required for that traffic path.

### Edge Protection with WAF
An **AWS-managed WAF** is attached at the CloudFront layer to provide protective controls at the edge.

WAF logs are also retained for security visibility and audit purposes.

## Performance and Content Delivery

### CloudFront Caching Strategy
**Static assets** are cached at CloudFront to improve performance and reduce unnecessary backend load.

**Private or sensitive data is not cached**, which is important in environments where user-specific or regulated data should not be broadly stored at edge layers.

This split helps balance **performance** with **data handling discipline**.

## Monitoring, Alerting, and Response

### EC2 Health Monitoring
**CloudWatch** is used to monitor EC2 health and availability.

This helps detect when application instances go down or become unhealthy.

### Database Monitoring
CloudWatch monitoring is also configured for database-related conditions such as:

- connection failures
- database failures

### SNS Alerting
**SNS** is used to send alerts when defined failure conditions occur.

### Pre-Written Diagnostic Messaging
Monitoring workflows include **pre-written alert messages** that describe what may be wrong.

This helps reduce response time by giving operators a faster starting point during an incident.

### Incident Response Lambda
An **incident response Lambda function** is included and granted the required access through an **IAM role**.

This supports a more automated response model and reflects the kind of operational readiness expected in sensitive environments.

## Logging, Auditability, and Compliance-Friendly Retention

### ALB Logging
**ALB access logs** are stored in **S3**.

### CloudFront Logging
**CloudFront logs** are also stored in **S3**.

### WAF Logging
**WAF logs** are retained in **S3** as well.

### SIEM Pipeline Preparation
Some logs are prepared for downstream security analysis through **Kinesis Data Firehose**.

This design supports forwarding logs into external analysis platforms such as:

- Splunk
- other SIEM tools

This is important for environments that need stronger visibility, incident investigation support, and long-term log handling practices.

## Infrastructure as Code

This project is managed with **Terraform**.

Terraform is used to define and manage major parts of the environment, including:

- networking
- DNS
- certificates
- CloudFront
- load balancing
- IAM roles
- monitoring
- alerting
- logging
- VPN-related configuration
- secrets and configuration references

Using Terraform helps make the infrastructure repeatable, auditable, and easier to evolve over time.

## Why This Project Matters

This project demonstrates hands-on ability in building a **security-first multi-cloud infrastructure** that is relevant to healthcare-style environments where infrastructure must be:

- resilient
- observable
- tightly controlled
- audit-friendly
- designed with sensitive systems in mind

It highlights work across:

- AWS and GCP hybrid networking
- VPN architecture
- Transit Gateway design
- secure secret retrieval
- CloudFront and WAF protection
- compliance-friendly log retention
- monitoring and incident response automation
- Terraform-driven infrastructure delivery

## Documentation Status

This repository is still being documented.

Additional project evidence will be added later, including:

- architecture diagrams
- Terraform structure screenshots
- deployment evidence
- monitoring screenshots
- alerting evidence
- logging pipeline evidence
- security control validation
- test results

**Evidence will be posted later.**

## Important Note

This repository presents a **healthcare-oriented secure infrastructure design**, but formal compliance claims, certifications, and validation artifacts are **not yet included in the documentation**.

The current focus is on showing the architecture, security model, operational controls, and engineering approach.

## Future Improvements

Planned future additions may include:

- fuller architecture diagrams
- sanitized Terraform module breakdowns
- expanded SIEM integration examples
- failover testing evidence
- more detailed operational runbooks
- stronger incident response automation workflows
- deeper documentation around cross-cloud recovery and resiliency

## Author

**Gavin Fogwe**

---

> A hybrid AWS and GCP infrastructure project designed around secure connectivity, internal-only service protection, observability, controlled ingress, and healthcare-style operational requirements. More documentation and evidence will be added later.
