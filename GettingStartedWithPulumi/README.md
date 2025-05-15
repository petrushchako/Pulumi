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

<br><br><br>

# **Your First Pulumi Project**

## Version Check

This version was created by using:
  - Pulumi  
  - Pulumi CLI 1.0.0
  - Pulumi Provider Libraries 1.0.0

<br><br><br>

## Introducing Pulumi
### Overview
Pulumi is an **Infrastructure as Code (IaC)** tool that enables you to define, deploy, and manage infrastructure using **real programming languages** and source control systems.

### Key Characteristics of Infrastructure as Code
* **Text-based configuration** stored in source control (e.g., Git, Mercurial)
* **Automation-friendly**: No need for manual clicks or console interaction
* Replaces the need to configure **physical hardware** (e.g., routers, servers)

<br>

### Why Pulumi Stands Out
#### 1. Broad Multi-Resource Support
Pulumi can manage:
* **Cloud resources**: AWS, Azure, Google Cloud
* **Databases**: PostgreSQL, MySQL (e.g., database users and schemas)
* **Kubernetes**: Services, pods, namespaces
* **30+ Providers**: Supports multi-cloud and hybrid environments

> Unlike AWS CloudFormation or Azure ARM templates, Pulumi is **not limited to a single vendor**.

<br>

#### 2. Use of Familiar Programming Languages
Define infrastructure in:
* **C#**
* **Python**
* **JavaScript / TypeScript**
* **Go**

##### Benefits:
* Use your favorite **IDEs** and **language tooling**
* Apply familiar **best practices**, **code organization**, and **libraries**
* Maintain and refactor infrastructure like traditional software projects

<br>

### Summary
Pulumi brings together:
1. **Multi-cloud resource management** in a consistent way.
2. **Mainstream programming languages** to define and manage infrastructure.

These two pillars make Pulumi a **powerful and flexible tool** for modern infrastructure development.


<br><br><br>


## Creating and Running a Pulumi Project
### 1. **Pulumi Setup**
* Go to **[pulumi.com](https://pulumi.com)** and click **Get Started**.
* Choose your **cloud provider** (e.g., Google Cloud).
* Follow platform-specific install instructions:
  * **Windows**: Use **Chocolatey** (`choco install pulumi`)
  * **macOS**: Use **Homebrew** (`brew install pulumi`)
  * **Linux**: Use the **install script** provided

<br>

### 2. **Google Cloud Setup**
* Ensure you have a **Google Cloud Project**:
  * Visit: [console.cloud.google.com](https://console.cloud.google.com)
  * Use the **drop-down menu** to create/select a project
* Enable programmatic access:

  * Default: Use `gcloud auth login`
  * Recommended: Use a **Service Account** (link available in setup docs)

<br>

### 3. **Create a Pulumi Project**
* Open a terminal and navigate to your project directory
* Run:

  ```bash
  pulumi new
  ```
* Log in to Pulumi (free for personal use)
* Choose the project template:
  * Example: `gcp-csharp` (for C# on Google Cloud)
* Enter:

  * **Project name** (default = folder name)
  * **Google Cloud project ID** (from GCP console)

<br>

### 4. **Deploying Infrastructure**
* Run:

  ```bash
  pulumi up
  ```
* Pulumi shows a **preview** of actions (e.g., creating a storage bucket)
* Confirm with `yes` to deploy
* Verify the resource in Google Cloud Console under **Storage**

<br>

### 5. **Destroying Infrastructure**
* Run:
  ```bash
  pulumi destroy
  ```
* This removes all Pulumi-managed resources
* Confirm by checking the GCP console (e.g., bucket should be gone)

<br>

### Summary
| Action            | Command          | Description                        |
| ----------------- | ---------------- | ---------------------------------- |
| Create project    | `pulumi new`     | Initializes a new Pulumi project   |
| Deploy resources  | `pulumi up`      | Provisions cloud infrastructure    |
| Destroy resources | `pulumi destroy` | Tears down deployed infrastructure |

Pulumi makes it easy to define, preview, deploy, and destroy infrastructure using code in a language you're comfortable with.

<br>

Here is the Markdown-formatted summary of the lecture titled **Pulumi’s Declarative Model and Infrastructure State Management**:

---

## Pulumi’s Declarative Model and Infrastructure State Management
### 1. **Declarative vs Imperative Models**
* **Declarative model** (Pulumi):
  * You **describe the desired state**, and Pulumi **ensures** the infrastructure matches.
  * Example:
    * Want a blue triangle → Pulumi creates one if it doesn't exist.
    * Change to orange triangle → Pulumi updates the color.
    * No change → Pulumi does nothing.
* **Imperative model**:
  * You **write step-by-step instructions** to manage resources.
  * Requires checks for existence, attributes (e.g., color), and more.
  * Becomes **complex and error-prone** over time.

<br>

### 2. **Example in Action with Pulumi + C#**
* **Pulumi project** created earlier includes a class `MyStack` that defines a single GCP bucket.
* Command used to deploy:
  ```bash
  pulumi up --yes
  ```
* Bucket is created on Google Cloud as declared.

<br>

### 3. **Updating Infrastructure Declaratively**
* Add a **label** to the bucket in C#:
  ```csharp
  Labels = { { "declarative", "absolutely" } }
  ```

* Run `pulumi up`:
  * Pulumi previews the change: `update` on the bucket.
  * It only updates the **label** field.

* View in GCP Console → Label shows correctly.
* Modify label value to `"of-course"`:

  ```csharp
  Labels = { { "declarative", "of-course" } }
  ```

* Run `pulumi up` again → Pulumi applies only the difference.
* Final run with no changes results in:

  * **No actions taken**, since declared state = actual state.
