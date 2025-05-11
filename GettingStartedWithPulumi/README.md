# Getting Started With Pulumi
Managing cloud infrastructure can often be a challenge of wrangling obtuse configuration files or manually configuring resources through your cloud provider’s web console. In this course, Getting Started with Pulumi, you’ll learn the basics of managing cloud infrastructure using your favorite programming language. First, you’ll explore creating, modifying, and destroying cloud resources in providers like AWS, GCP, and Azure. Next, you’ll discover how to create dependencies between resources – servers, databases, storage, DNS, and more – so that they are dynamically configured to work together. Finally, you’ll learn how to make reusable groups of resources so that you can create and destroy independent replicas of your environments for testing or demos. When you’re finished with this course, you’ll be ready to start using Pulumi to manage your cloud infrastructure.

<br><br>

### Course content	
- **Course Overview**
- **Your First Pulumi Project**
  - Version Check
  - Introducing Pulumi
  - Creating and Running a Pulumi Project
  - Declarative Infrastructure
  - Course Plan
- **Managing Resources with Relationships**
  - Introduction to Resource Dependencies
  - Deploying a Static HTML File
  - Recovering from Failed Deployments
  - Resources, Dependencies, Inputs, and Outputs
  - Data-driven Resources
  - Refactoring Pulumi Code
- **Incorporating Multiple Providers**
  - Resources and Providers
  - Creating a Hosted Postgres Database
  - Providers and Their Configuration
  - Managing Complexity
- **Troubleshooting When Deployments Fail**
  - Learning from Manually-created Resources
  - Pulumi Documentation
  - Using Terraform Documentation with Pulumi
  - End-to-end DNS and SSL Configuration
- **Designing Pulumi Stacks for Reuse**
  - Introducing Stacks
  - Varying Stack Details Using Configuration
  - Continuous Deployment and Temporary Environments
  - Course Review


## Course Overview
> Floyd May
> Independent Software Crafter
> @softwarefloyd canyon-trail.com

### Course Overview
Pulumi is a modern infrastructure as code (IaC) tool that allows developers to manage infrastructure using general-purpose programming languages.

#### Supported Technologies
Pulumi supports a wide range of technologies, including:

* Amazon Web Services (AWS)
* Google Cloud Platform (GCP)
* Microsoft Azure
* Kubernetes
* PostgreSQL
* And many more

#### Supported Languages
Pulumi enables you to define infrastructure using:

* C#
* Python
* TypeScript
* Go

### What You Will Learn
* Creating and destroying infrastructure resources
* Managing dependency relationships between resources
* Handling complexity as infrastructure scales
* Integrating Pulumi with continuous deployment workflows

### Prerequisites
* Familiarity with at least one of the following languages: C#, TypeScript, or Python
* Basic command-line skills

### Course Outcome
By the end of this course, you will have a solid understanding of how to manage infrastructure as code using Pulumi.


<hr><br><br><br>

## Managing Resources with Relationships

<br>

## Introduction to Resource Dependencies
### Overview
This module focuses on using Pulumi to manage **multiple infrastructure resources** that have **dependency relationships** between them.

### Use Case: Carved Rock Training Program Website
We are working on deploying the front end of a static website and connecting it to a serverless back end.

#### Goals
* Host the website's **front end** in a **cloud storage bucket**
* Incorporate a **serverless function** as the **back end** for dynamic functionality

#### Technology Stack
* **Cloud Provider:** Google Cloud Platform (GCP)
  * Equivalent functionality exists in **AWS** and **Azure**
  * Pulumi supports all of these providers equally

### Current Status
* The development team has built a **static website**
* Future updates will introduce dynamic interactions with the back end

### Implementation Steps
1. **Create a cloud storage bucket**
2. **Upload website assets** into the bucket:
   * HTML
   * CSS
   * JavaScript
   * Images

### Key Concept: Resource Relationships
* Each file (HTML, CSS, etc.) is treated as a **Pulumi-managed resource**
* These files (bucket objects) **depend** on the existence of the bucket
* Pulumi models and respects this **dependency** automatically

<br><br><br>