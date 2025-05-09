Authorization Configuration for Pulumi with AWS
When working with Pulumi and AWS, proper authorization is crucial to ensure that Pulumi can create, modify, and delete resources in your AWS account. AWS uses Identity and Access Management (IAM) to manage access to its services. Here's a breakdown of how to configure authorization for Pulumi:
1. Understanding IAM Roles and Users
IAM User: An entity that you create in AWS to represent a person or application that interacts with AWS. Users have permanent credentials (access keys).
IAM Role: An IAM identity that you can assume. Roles don't have permanent credentials. Instead, when you assume a role, you get temporary security credentials. Roles are often used to grant permissions to applications or services.
2. Pulumi's Authentication Methods
Pulumi primarily uses these methods to authenticate with AWS:
Using AWS Credentials Directly:
Static Credentials: You can configure Pulumi to use your AWS access key ID and secret access key. This is generally not recommended for production environments due to the security risks of managing long-term credentials.
Environment Variables:
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS Configuration File (~/.aws/config or C:\Users\USERNAME\.aws\config): You can store profiles with your credentials here.
Credential Chain: Pulumi uses the AWS SDK's default credential provider chain, which searches for credentials in the following order:
Environment variables
AWS configuration file
IAM role for EC2 instance (if running on EC2)
IAM role for ECS task (if running on ECS)
Using IAM Roles:
Recommended Approach: The most secure way to grant Pulumi permissions is by using IAM roles.
How it Works:
Create an IAM role with the necessary permissions.
Configure the environment where Pulumi runs (e.g., your local machine, a CI/CD system, an EC2 instance) to assume that role.
Pulumi uses the temporary credentials obtained from assuming the role to interact with AWS.
3. Steps to Configure Authorization with IAM Roles
Here's a detailed guide:
3.1 Create an IAM Role
Go to the IAM console in the AWS Management Console.
Click on "Roles" in the left navigation pane.
Click "Create role."
Select the entity that will assume this role:
AWS service: If Pulumi will run on an AWS service (e.g., EC2, ECS), select that service.
Another AWS account: If you want to grant permissions to Pulumi running in a different AWS account.
Web identity: For federated identities.
Define the trust policy: This policy specifies which entities are allowed to assume the role. For example, if Pulumi will be running from your local machine, you might create a role that can be assumed by your IAM user. If Pulumi runs in GitHub Actions, you configure the trust policy so GitHub Actions can assume the role.
Attach policies: Select the IAM policies that grant the necessary permissions to the role. You can use AWS-managed policies (e.g., AdministratorAccess - use with caution, least privilege is best) or create custom policies. See the "Least Privilege Principle" below.
Name the role and provide a description.
Create the role.
3.2 Configure Permissions (Least Privilege Principle)
Principle of Least Privilege: Grant only the minimum necessary permissions. Avoid using overly permissive policies like AdministratorAccess in production.
Custom Policies: Create custom IAM policies tailored to the specific resources that Pulumi needs to manage. This provides the best security.
Example Policy: Here's an example of a custom policy that grants Pulumi permissions to create, read, update, and delete S3 buckets and EC2 instances:
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:CreateBucket",
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::my-pulumi-bucket",
                "arn:aws:s3:::my-pulumi-bucket/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:RunInstances",
                "ec2:DescribeInstances",
                "ec2:TerminateInstances",
                "ec2:CreateTags",
                "ec2:DescribeSecurityGroups",
                "ec2:CreateSecurityGroup",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:DeleteSecurityGroup"
            ],
            "Resource": [
                "arn:aws:ec2:us-east-1:123456789012:instance/*",
                "arn:aws:ec2:us-east-1:123456789012:security-group/*"
            ]
        }
    ]
}


Policy Scoping: Use specific ARNs (Amazon Resource Names) to restrict the policy to particular resources. Avoid using wildcards (*) excessively.
3.3 Configure Pulumi to Assume the Role
The method for configuring Pulumi to assume the role depends on where Pulumi is running:
Local Machine:
AWS CLI: Use the AWS CLI to configure a profile that assumes the role.
aws configure --profile pulumi-role
aws sts assume-role --role-arn arn:aws:iam::123456789012:role/PulumiRole --role-session-name PulumiSession
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...


Environment Variables: Set the AWS_PROFILE environment variable to the name of the profile you created.
export AWS_PROFILE=pulumi-role


EC2 Instance:
Attach an instance profile to the EC2 instance. The instance profile contains the IAM role.
GitHub Actions:
Use a GitHub Action like aws-actions/configure-aws-credentials to configure credentials. You'll need to provide the role ARN and configure the action to assume the role.
4. Best Practices
Use IAM Roles: Always prefer IAM roles over static credentials for enhanced security.
Least Privilege: Grant only the necessary permissions to the IAM role or user.
Regularly Rotate Credentials: If you must use static credentials (not recommended), rotate them frequently.
Securely Store Credentials: Never hardcode credentials in your Pulumi code. Use environment variables, configuration files, or a secrets manager.
Use pulumi config set --secret: When setting sensitive configuration values, use the --secret flag to encrypt them in the Pulumi state file.
Monitor Access: Use AWS CloudTrail to log API calls and monitor who is accessing your AWS resources.
By following these guidelines, you can ensure that your Pulumi deployments are secure and adhere to AWS best practices for authorization.
