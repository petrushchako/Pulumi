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

## Deployinh a Static HTML File
### Project Structure
The project is organized into two main directories:
* **`frontend/`**
  Contains the front-end application code. Currently, this includes:
  * A `build/` folder with a placeholder file: `helloworld.html`
* **`infrastructure/`**
  Contains the Pulumi program for managing infrastructure.

### Step-by-Step Deployment

#### 1. Initialize the Pulumi Project
* Navigate to the `infrastructure/` folder.
* Run `pulumi new` and select the template: `gcp-csharp`
* Project name: `carvedrock-training`
* Provide a description and accept the default stack name.
* Retrieve and set your GCP project ID in the configuration.

#### 2. Configure the Pulumi Program
* Open the generated C# program.
* Rename the bucket resource to `frontend-files`.

#### 3. Upload Front-End File to the Bucket
* Goal: Upload `helloworld.html` from the `frontend/build/` folder to the bucket.

##### File Upload Process
* Use **`FileAsset`** to load the file.
  `FileAsset` is part of the Pulumi API, allowing consistent file upload across cloud providers.
* Create a **`BucketObject`** to represent the file inside the bucket.
  * The Pulumi name of the object is separate from the actual object name in GCP.
  * Use `BucketObjectArgs` to configure:
    * `Name`: the target object name
    * `Source`: set to the `FileAsset`
    * `Bucket`: reference the `frontend-files` bucket

#### 4. Deploy the Configuration
* Run `pulumi up --yes` to apply the changes without manual confirmation.
* Verify the deployment in the Google Cloud Console:

  * Navigate to **Storage** → **frontend-files**
  * Confirm that `helloworld.html` appears in the bucket

#### 5. Public Access Limitation
* Note: The uploaded file is not publicly accessible by default.
* Although public access can be enabled via the Cloud Console Permissions UI, this goes against the principle of managing infrastructure through code.

### Next Step
To properly serve the static website to the public, access permissions will need to be handled declaratively through Pulumi rather than manually.

<br><br><br>

## Recovering from Failed Deployments
### Granting Public Access with IAM Bindings in Pulumi
#### Objective
Make a static HTML file hosted in a Google Cloud Storage bucket publicly accessible by configuring appropriate IAM permissions using Pulumi.

### Step-by-Step Process
#### 1. Problem: File Not Yet Public
* Although the HTML file is uploaded to the bucket, it is **not publicly accessible**.
* Required: IAM binding to allow public access to objects in the bucket.

<br>

### 2. Adding a Bucket IAM Binding
* A **Bucket IAM Binding** binds a role to members for a storage bucket.
* Add the binding to the Pulumi C# program:
  * Reference the correct bucket
  * Use a **standard IAM role** like `roles/storage.objectViewer`
  * Assign it to a **public member** like `allUsers`

```csharp
new BucketIAMBinding("publicReadBinding", new BucketIAMBindingArgs
{
    Bucket = bucket.Name, // correct bucket reference
    Role = "roles/storage.objectViewer",
    Members = { "allUsers" }
});
```

<br>

### 3. Common Mistake and Debugging
#### Issue:
* An error occurred when referencing the **bucket object** instead of the **bucket** itself.

#### Error Message:
```
Error retrieving IAM policy for storage bucket b/helloworld.html
```

#### Resolution:
* Correct the code to reference the actual bucket (not the file object inside the bucket).
* Demonstrates a typical **trial-and-error** flow in Pulumi and Infrastructure as Code (IaC).

<br>

### 4. Deployment Conflict
#### Scenario:
* `pulumi up` was interrupted with `Ctrl+C`
* On rerun, Pulumi throws:

  ```
  error: [409] Conflict: Another update is currently in progress
  ```

#### Fix:
* Use the `pulumi cancel` command to clear the blocked update:
  ```bash
  pulumi cancel
  ```
* Enter the stack name when prompted.

<br>

### 5. Verifying Public Access
* After successfully deploying the IAM binding:
  * Check the bucket in GCP Console → should display **"Public to internet"**
  * Copy the file URL and open it in:
    * A regular browser tab (works)
    * An incognito window (also works, confirms public access)

<br>

### 6. Team Considerations with `pulumi cancel`
* Pulumi allows **only one update per stack** at a time.
* Concurrent updates (e.g., from multiple developers or CI pipelines) are blocked to **prevent infrastructure corruption**.
* `pulumi cancel` can be used to clear a stuck update regardless of origin:
  * Be cautious using it in **team or CI environments**
  * It can terminate active updates from others if misused
