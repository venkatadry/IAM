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
| Feature                         | Description                                                                 |
|----------------------------------|-----------------------------------------------------------------------------|
| **Created by AWS**              | Maintained and updated by AWS.                                              |
| **Predefined use cases**        | Include policies like `AmazonS3ReadOnlyAccess`, `AdministratorAccess`, etc. |
| **Attachable to identities**    | Can be attached to IAM **users**, **groups**, and **roles**.               |
| **Not customizable**            | You **cannot modify** the permissions in these policies.                   |
| **Auto-updated**                | AWS **updates** the policies to support **new services and features**.     |
| **Quick to use**                | Saves time compared to writing custom policies.                             | 

Some examples of AWS Managed Policies include:

***Administrator Access:*** Provides full access to AWS services and resources. This policy is intended for administrators who need complete control over all resources.

***Read Only Access:*** Grants read-only access to all AWS services and resources. Users with this policy can view configurations and details but cannot make changes.

***AmazonS3FullAccess:*** Grants full access to Amazon S3, allowing users to perform any action on S3 buckets and objects.
These managed policies are designed to make it easy to assign common permissions without manually specifying each individual permission. You can attach these policies directly to IAM users, groups, or roles

***2.Customer managed policies***
![image](https://github.com/user-attachments/assets/7deb2d08-9a4c-4db6-8ed3-e6e9320082ce)

| Policy Type          | Created By      | Customizable | Reusable | Best For                                 |
|----------------------|------------------|--------------|----------|------------------------------------------|
| Customer Managed     | You              | ✅ Yes       | ✅ Yes   | Custom, organization-specific use cases  |
| AWS Managed          | AWS              | ❌ No        | ✅ Yes   | Common, general-purpose use cases        |
| Inline               | You (per entity) | ✅ Yes       | ❌ No    | One-off, entity-specific permissions     |

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

***1. Least Privilege Enforcement***
Scenario: A startup wants to ensure developers only have access to their project’s resources.

Solution:
Define a customer-managed policy restricting access to:

Specific S3 buckets (arn:aws:s3:::project-team1-*)
Only certain EC2 tags ("Condition": {"StringEquals": {"aws:ResourceTag/Project": "Alpha"}})

***2. Compliance & Security Policies***
Scenario: A financial firm must enforce MFA (Multi-Factor Authentication) for sensitive operations.
Solution:
Create a customer-managed policy requiring MFA for actions like:

```json
{
    "Effect": "Deny",
    "Action": ["s3:DeleteBucket", "iam:ChangePassword"],
    "Resource": "*",
    "Condition": {"BoolIfExists": {"aws:MultiFactorAuthPresent": false}}
}
```


Apply this to all IAM users.

***3. Cross-Account Access Delegation***
Scenario: A company has a central AWS account for logging and wants to allow other accounts to send logs.

Solution:
Define a customer-managed policy in the central account allowing:

```
json
{
    "Effect": "Allow",
    "Action": ["logs:CreateLogStream", "logs:PutLogEvents"],
    "Resource": "arn:aws:logs:us-east-1:123456789012:log-group:Prod-Logs:*"
}
```

Attach this to an IAM role trusted by other accounts.

***4. Temporary Access for Contractors***
Scenario: A third-party vendor needs time-bound access to an S3 bucket.

Solution:
Create a customer-managed policy with a time-based condition:
```
json
{
    "Effect": "Allow",
    "Action": ["s3:GetObject"],
    "Resource": "arn:aws:s3:::confidential-data/*",
    "Condition": {
        "DateLessThan": {"aws:CurrentTime": "2024-12-31T23:59:59Z"}
    }
}
```
Attach to the contractor’s IAM role.

***5. Environment-Specific Permissions (Dev/Prod)***
Scenario: A company wants to prevent accidental deletions in production.

Solution:
Define a customer-managed policy that:
Allows full access in Dev
```
("Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*")
```

Denies Delete* actions in Prod
```
("Resource": "arn:aws:ec2:us-east-1:123456789012:instance/prod-*")
```
```{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyTerminateEC2InProd",
            "Effect": "Deny",
            "Action": "ec2:TerminateInstances",
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/Environment": "prod"
                }
            }
        }
    ]
}
```
Explanation:

It denies the ec2:TerminateInstances action.

Only if the instance has the tag Environment=prod.

Other EC2 operations (like start/stop) are still allowed unless you deny them too


***7. Automated Backup Policies***
Scenario: An IT team wants to allow automated backups but restrict manual deletions.

Solution:

Create a policy allowing backup:StartBackupJob but denying backup:DeleteBackupVault.

***8. Cost Optimization (Restricting Region/Service Usage)***
Scenario: A company wants to limit deployments to specific regions to control costs.

Solution:

Define a customer-managed policy with:

```
json
{
    "Effect": "Deny",
    "Action": ["ec2:RunInstances", "rds:CreateDBInstance"],
    "NotResource": ["arn:aws:ec2:us-east-1::*", "arn:aws:rds:us-east-1::*"]
}
```





***⇒ b) Inline policies:***

![image](https://github.com/user-attachments/assets/877f4500-2a2e-4d22-a21b-1aaad7452c07)
| Feature                     | Inline Policies                   | Managed Policies                        |
|-----------------------------|------------------------------------|------------------------------------------|
| **Reusability**             | ❌ No                              | ✅ Yes                                   |
| **Lifecycle**               | Tied to identity                   | Separate from identity                   |
| **Use Case**                | Unique, specific needs             | Broad, reusable permissions              |
| **Management complexity**   | Higher (at scale)                  | Lower (centralized)                      |
| **Customization**           | ✅ Fully customizable              | ❌ AWS managed policies not editable     |

You can tailor the permissions precisely to the needs of that entity without affecting others.

Consider an example of an inline policy in JSON format:

```{
 "Version": "2012–10–17",
 "Statement": [
 {
 "Effect": "Allow",
 "Action": [
 "s3:GetObject",
 "s3:ListBucket"
 ],
 "Resource": [
 "arn:aws:s3:::test-bucket/*",
 "arn:aws:s3:::test-bucket"
 ]
 },
 {
 "Effect": "Deny",
 "Action": "s3:DeleteObject",
 "Resource": "arn:aws:s3:::test-bucket/sensitive-data.txt"
 }
 ]
}
```
In this example:

Allows the IAM entity to get objects and list the contents of the “test-bucket” in Amazon S3.
Denies the IAM entity the ability to delete the “sensitive-data.txt” object within the “test-bucket.”

***✅ Inline Policy***
Defined: Directly within a single IAM identity (user, group, or role).


***⇨ Choosing between managed policies and inline policies***

| Feature                   | Managed Policies                       | Inline Policies                         |
|---------------------------|----------------------------------------|-----------------------------------------|
| Scope                     | Reusable across entities               | Specific to one IAM entity              |
| Management                | Centralized                            | Decentralized                           |
| Use Case                  | Broad access needs                     | Fine-grained, entity-specific access    |
| Lifecycle Independence    | Survives entity deletion               | Deleted with the entity                 |
| Complexity                | Easier to manage                       | Higher complexity in large environments |

Use ***Managed Policies*** for most cases due to their maintainability and alignment with best practices.
Use ***Inline Policies*** when unique, granular permissions are required for a specific entity.


***2️⃣ Resource-based policies***


![image](https://github.com/user-attachments/assets/84cdd5b6-2da4-432b-b8b1-e91f0c8f7525)

Resource-based policies in AWS are a type of access policy that is associated with an AWS resource. These policies define who has access to the resource and what actions they can perform on it. Unlike identity-based policies, which are attached to IAM users, groups, or roles, resource-based policies are attached directly to the AWS resource itself.

***Here are key aspects of resource-based policies:***

***Resource Ownership:***

![image](https://github.com/user-attachments/assets/2682bbf8-f571-455c-a800-3042ada4cfb5)
Resource-based policies are typically associated with AWS resources like Amazon S3 buckets, Lambda functions, SNS topics, and others.
They allow resource owners to control access to their resources and specify who (accounts, users, or entities) can interact with the resource.
***2. JSON Policy Documents:***

Resource-based policies are written in JSON format, similar to identity-based policies.
These policy documents specify the permissions for the resource, including the allowed actions and the entities (principals) that are granted or denied access.

***3. Cross-Account Access:***
Resource-based policies often facilitate cross-account access, allowing resources to be shared across AWS accounts.
For example, an S3 bucket owner can grant permissions to another AWS account to read or write objects in that bucket.

***4. Fine-Grained Control:***

Resource-based policies provide fine-grained control over who can access the resource and what operations they can perform.
They are commonly used to control access to shared resources in a multi-account environment.

***5. Supported AWS Services:***
Various AWS services support resource-based policies, including S3, Lambda, SNS, SQS, KMS, and more.
Each service’s resource-based policy may have specific elements tailored to the nature of the service.
Here’s a simplified example of an S3 bucket resource-based policy:

```{
 "Version": "2012–10–17",
 "Statement": [
 {
 "Effect": "Allow",
 "Principal": {
 "AWS": "arn:aws:iam::account-ID-without-hyphens:root"
 },
 "Action": "s3:GetObject",
 "Resource": "arn:aws:s3:::test-bucket/*"
 },
 {
 "Effect": "Allow",
 "Principal": {
 "AWS": "arn:aws:iam::another-account-ID:root"
 },
 "Action": "s3:PutObject",
 "Resource": "arn:aws:s3:::test-bucket/uploaded-files/*"
 }
 ]
}
```

In this example:

The first statement allows the root user of the account with account-ID-without-hyphens to retrieve objects from the "test-bucket."
The second statement allows the root user of another AWS account (another-account-ID) to upload objects to the "uploaded-files" directory within the same bucket.
Resource-based policies play a crucial role in managing access to AWS resources, especially in scenarios where sharing resources across accounts or defining access at the resource level is required. They provide a flexible and granular way to control access to resources within the AWS environment.

***Example : Lambda Function Resource Policy Allowing EventBridge to Invoke It***


```{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowEventBridgeInvoke",
      "Effect": "Allow",
      "Principal": {
        "Service": "events.amazonaws.com"
      },
      "Action": "lambda:InvokeFunction",
      "Resource": "arn:aws:lambda:us-east-1:111122223333:function:my-function"
    }
  ]
}
```
.

***3️⃣ Permissions boundaries***

Permissions boundaries in AWS IAM serve as a crucial tool for enforcing granular control over the permissions of IAM entities, including users, groups, and roles. Here are key characteristics and benefits of permissions boundaries:

***Maximum Permissions Control:***
Permission boundaries in AWS IAM set the maximum permissions for IAM entities by attaching an IAM policy. This prevents entities from gaining excessive permissions, improving security and compliance.

***2. Limiting Access to Resources:***
Permission boundaries are crucial for restricting access to specific AWS resources or services, enforcing security policies, and thwarting unauthorized access to sensitive resources.

***3. Hierarchy in IAM Entities:***
Permission boundaries are integrated within hierarchical IAM entity structures, enabling precise permission control. For example, a user may have a permission boundary set, constraining their permissions despite belonging to groups with wider access. This hierarchical method ensures efficient management and enforcement of permissions across various levels of IAM entities.

***4. Granular Control:***
Permission boundaries offer granular control over the actions that an IAM entity can execute on specific resources. By following the principle of least privilege, organizations can establish precise permissions across different levels of the IAM hierarchy, thus reducing the potential for unauthorized access.

Note:

Permission boundaries leverage managed policies to establish the maximum permissions allocated to an IAM entity. An entity’s permissions boundary confines its actions to the intersection of permissions permitted by both its identity-based policies and permission boundaries. This alignment ensures that entities operate within defined boundaries, adhering to security best practices and organizational policies.

***4️⃣ Organization Policies (Service Control Policies)***


Organization policies, also known as Service Control Policies (SCPs), are an essential feature of AWS Organizations. They serve as a mechanism for governing permissions and access controls across the entire AWS Organization. SCPs enable administrators to establish rules that restrict or allow access to specific AWS services or actions, helping to enforce compliance, security, and governance standards.

***Features:***

***Centralized Governance:*** SCPs provide a centralized approach to managing permissions across all member accounts within an AWS Organization.
Access Control: Administrators can use SCPs to define granular permissions, restricting access to certain AWS services or actions based on organizational policies.
***Hierarchical Structure:*** SCPs can be applied at different levels within the organization’s hierarchy, allowing for fine-grained control over permissions at the organization, organizational unit (OU), or individual account level.
***Compliance Enforcement:*** SCPs help enforce compliance standards by ensuring that access to sensitive services or actions is limited to authorized entities only.
***Default Deny Principle:*** SCPs adhere to the “deny-by-default” principle, meaning that all actions are denied unless explicitly allowed by the policy.
***Impact on IAM Policies:*** SCPs can override or augment the permissions granted by IAM policies attached to individual IAM entities within member accounts.
***Use Cases:***

***Security Standards:*** SCPs are valuable for enforcing security standards across the organization, ensuring that only authorized actions are permitted and mitigating the risk of unauthorized access.
***Cost Controls:*** SCPs can be used to manage costs by restricting access to expensive AWS services or actions that may lead to unnecessary expenses.
***Compliance Requirements:*** Organizations subject to regulatory compliance requirements can use SCPs to enforce access controls and data protection measures mandated by regulations.
***Custom Governance Policies:*** SCPs can be tailored to meet the specific security and governance needs of the organization, providing flexibility in policy enforcement while maintaining compliance.
In summary, Organization Policies (Service Control Policies) offer a robust framework for controlling access to AWS resources within AWS Organizations, facilitating security, compliance, and efficient resource management across the entire organization’s AWS environment.

***5️⃣ Access Control Lists (ACLs)***

Access Control Lists (ACLs) are a mechanism used in computer security to define and manage permissions at the resource level. They specify which users or system processes are granted access to objects, as well as what operations they are allowed to perform on those objects. ACLs are typically associated with files, directories, network resources, or other system resources, and they provide a granular level of control over access permissions.

ACLs play a crucial role in managing permissions by allowing administrators to define who can access specific resources and what actions they can perform on those resources. By configuring ACLs, administrators can enforce security policies, restrict unauthorized access, and ensure compliance with regulatory requirements. ACLs complement other access control mechanisms such as user roles, groups, and policies, providing an additional layer of control at the resource level.

***Common Uses and Impact on Access Control:***

***File Systems:*** In file systems, ACLs are used to control access to files and directories. For example, an ACL on a file may specify which users or groups are allowed to read, write, or execute the file. This allows administrators to restrict access to sensitive files or directories based on user identity or group membership.

***Network Resources:*** ACLs are commonly used in network devices such as routers, switches, and firewalls to control traffic flow and enforce security policies. By configuring ACLs on network interfaces or ports, administrators can filter incoming and outgoing traffic based on source and destination IP addresses, protocols, and ports.

***Cloud Services:*** In cloud computing environments, ACLs are used to control access to cloud resources such as storage buckets, databases, and virtual machines. By defining ACLs on these resources, administrators can manage access permissions for users, applications, and services accessing the cloud infrastructure.
Database Systems: ACLs are employed in database management systems to regulate access to database objects such as tables, views, and stored procedures. Database ACLs specify which users or roles have permission to perform operations such as querying data, modifying schema, or executing SQL commands.

***Web Servers:*** ACLs are utilized in web servers to control access to web content and applications. By configuring ACLs on directories or files within the web server's document root, administrators can restrict access to sensitive files, directories, or web applications based on client IP addresses, authentication credentials, or other criteria.
Overall, ACLs provide a flexible and powerful mechanism for managing permissions at the resource level, allowing administrators to enforce access control policies tailored to their specific security requirements. However, it's essential to carefully design and configure ACLs to avoid unintended access permissions or security vulnerabilities. Regular audits and reviews of ACL configurations are recommended to ensure effective access control and mitigate security risks.

***6️⃣ Session Policies***

Session policies in AWS Identity and Access Management (IAM) are temporary permissions that are dynamically applied to a user or role during a specific session. Unlike identity-based policies, which are attached to IAM entities such as users, groups, or roles, session policies are not stored or attached directly. Instead, they are passed as parameters when initiating a session, typically through services like AWS Security Token Service (STS) or AWS Identity Federation.

Features:

***Temporary Permissions:*** Session policies provide temporary permissions that are only valid for the duration of a specific session. Once the session expires, the permissions are automatically revoked.
***Dynamic Assignment:*** Session policies are dynamically applied to users or roles when they assume temporary credentials, allowing for flexible and on-demand access control.
***Limited Scope:*** Session policies can define a subset of permissions compared to permanent identity-based policies, providing a more restricted scope of access for specific tasks or operations.
***Flexible Access Control:*** Session policies enable fine-grained control over permissions, allowing administrators to tailor access rights based on the requirements of individual sessions or applications.
***Security Benefits:*** By providing temporary credentials with limited permissions, session policies help reduce the risk of unauthorized access and privilege escalation in the event of credential compromise.
***Auditing and Compliance:*** Session policies contribute to auditing and compliance efforts by providing visibility into the specific permissions granted during each session, aiding in tracking and monitoring access activities.
***Use Cases:***

***Temporary Access:*** Session policies are commonly used to grant temporary access to AWS resources for users or applications that require short-term permissions.

***Cross-Account Access:***
Organizations often use session policies to enable cross-account access, allowing users from one AWS account to assume roles in another account with specific permissions.

***3. Federated Identity Access:***
Session policies facilitate federated identity access scenarios, where users authenticate through external identity providers and receive temporary credentials with scoped permissions.

***4. Least Privilege Principle:***


By applying session policies with the principle of least privilege, organizations can ensure that users and applications only have access to the resources necessary to perform their intended tasks, reducing the risk of unauthorized actions.

In summary, session policies offer a flexible and secure approach to temporary access control in AWS IAM, providing organizations with the ability to grant precisely scoped permissions for short-lived sessions or specific use cases while maintaining strong security posture and compliance standards.

***🌟 Conclusion:***
IAM policies serve as the cornerstone of security infrastructure within AWS environments, offering essential control over access permissions and ensuring compliance with regulatory standards. Mastery of IAM policy management is essential for organizations seeking to fortify their cloud deployments against unauthorized access and maintain adherence to compliance requirements. By understanding the intricacies of IAM policies and employing best practices in their management, organizations can establish and uphold robust security postures in the dynamic landscape of cloud computing.


***Attached to:*** Only one identity.

Management: Created and managed 

