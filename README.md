# IAM
https://medium.com/@mohasina.clt/understanding-aws-iam-policies-a-comprehensive-guide-7e27c44711e2

***IAM (Identity and Access Management) in AWS is global.***

This means:
IAM users, groups, roles, and policies are not tied to a specific AWS region.
They exist and are shared across all regions.
When you create or update IAM resources, it automatically applies globally.
But — the permissions you set through IAM can control access to region-specific services (like EC2 in us-east-1, eu-west-1, etc.).
Here are short notes summarizing the key points about IAM policies in AWS:

---

**IAM Policies in AWS – Short Notes**

- **Definition**: IAM (Identity and Access Management) policies are JSON documents that define permissions for users, roles, or services in AWS.
- **Purpose**: They control *who* can access *what* AWS resources, and *under what conditions*, serving as a core component of AWS security.
- **Security Role**: IAM policies help protect sensitive data by preventing unauthorized access and ensuring only the right entities have the necessary permissions.
- **Compliance**: Proper policy management supports regulatory and compliance requirements by enforcing strict access controls.
- **Implementation**: Policies can be attached to users, groups, or roles,resources and are evaluated by AWS to allow or deny requests.

- **Best Practices**:
  - Follow the **Principle of Least Privilege**.
  - Regularly audit and refine policies.
  - Use **managed policies** for consistency and ease of updates.
  - Monitor with AWS services like CloudTrail and IAM Access Analyzer.

---

Would you like a visual summary or diagram as well?
![image](https://github.com/user-attachments/assets/3b116362-adc7-4000-9a72-b30d1d8e23d6)

By Default Everything is denoied for user in AWS
Account A can call resources in Account B we can use resource based policy
Aws managed policy updates will happen automatically?
if you lost access key you have to recreate it

Types of IAM Policies:
***1️⃣ Identity-based policies***

Identity-based policies in AWS Identity and Access Management (IAM) are policies that you attach to IAM identities, such as users, groups, or roles. These policies define the permissions that are granted to the associated identity. In other words, Identity-based policies are JSON permissions policy documents that control what actions an identity (users, groups of users, and roles) can perform, on which resources, and under what conditions.

There are two main types of identity-based policies: managed policies and inline policies.
***a) Managed policies:***
![image](https://github.com/user-attachments/assets/f80612d3-37c1-48b9-9152-e78a015c4719)

Managed policies are standalone policies that you can attach to multiple IAM identities or users, groups, and roles in your AWS account. They are created and managed independently from IAM users, groups, or roles. Managed policies are a powerful tool in IAM, providing a convenient way to assign permissions to IAM entities. They contribute to the principle of least privilege by allowing you to grant only the necessary permissions for specific tasks.

Managed policies come in two types:
***1. AWS managed policies:***
![image](https://github.com/user-attachments/assets/6599b558-df1e-407c-a0d6-62661535b12d)
These are predefined policies created and managed by AWS. Examples include the AWS managed policies for read-only access, administrator access, and more. You can attach these policies to your IAM identities.

AWS managed policies make it convenient for you to assign appropriate permissions to users, groups, and roles. It is faster than writing the policies yourself and includes permissions for many common use cases. You cannot change the permissions defined in AWS managed policies. And These policies are updated as new services and features are introduced. Some examples of AWS Managed Policies include:

***Administrator Access:*** Provides full access to AWS services and resources. This policy is intended for administrators who need complete control over all resources.
***Read Only Access:*** Grants read-only access to all AWS services and resources. Users with this policy can view configurations and details but cannot make changes.
***AmazonS3FullAccess:*** Grants full access to Amazon S3, allowing users to perform any action on S3 buckets and objects.
These managed policies are designed to make it easy to assign common permissions without manually specifying each individual permission. You can attach these policies directly to IAM users, groups, or roles

***2.Customer managed policies***
![image](https://github.com/user-attachments/assets/7deb2d08-9a4c-4db6-8ed3-e6e9320082ce)

Managed policies that you create and manage in your AWS account. Customer managed policies provide more precise control over your policies than AWS managed policies or in other words They provide more flexibility and control over the permissions you want to grant. You can create and attach customer managed policies to IAM identities.

You can create standalone policies in your own AWS account that you can attach to principal entities (users, groups, and roles). You create these customer managed policies for your specific use cases, and you can change and update them as often as you like. Like AWS managed policies, when you attach a policy to a principal entity, you give the entity the permissions that are defined in the policy. When you update permissions in the policy, the changes are applied to all principal entities that the policy is attached to.

Example of a Customer Managed Policy:

```{
 "Version": "2012–10–17",
 "Statement": [
 {
 "Effect": "Allow",
 "Action": "s3:ListBucket",
 "Resource": "arn:aws:s3:::test-bucket"
 },
 {
 "Effect": "Deny",
 "Action": "s3:*",
 "Resource": "arn:aws:s3:::test-bucket/sensitive-data/*"
 }
 ]
}

```

In this example:

Allows listing the contents of the “test-bucket” in Amazon S3.
Denies any S3 action on objects within the “sensitive-data” prefix of the “test-bucket.”

***⇒ b) Inline policies:***
![image](https://github.com/user-attachments/assets/877f4500-2a2e-4d22-a21b-1aaad7452c07)


