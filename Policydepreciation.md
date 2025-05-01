AWS Managed Policy Deprecation – Key Notes
✅ Policy Updates
AWS regularly updates managed policies.

New permissions are often added when AWS launches new services or features.

AWS may reduce permissions to improve security.

⚠️ Policy Deprecation
When permissions are removed, AWS deprecates the policy and releases a new version.

Policies are also deprecated if the associated service or feature is discontinued.

📩 Action on Deprecation Notification
If notified about a deprecated policy:

Review the changes immediately.

Update your workflows accordingly.

Replace the deprecated policy with the new one provided by AWS.

Attach the new policy to all relevant identities (users, groups, roles).

Detach the deprecated policy from those identities.

🛑 Characteristics of Deprecated Policies
Removed from the IAM documentation.

Still works for currently attached identities.

Shows a warning icon in the IAM console.

Cannot be attached to new identities.

Once detached, it cannot be reattached.

Disappears from the console once detached from all identities.

🔹 Example: Deprecation of AmazonEC2ContainerServiceFullAccess
📅 When?
Deprecated on: January 29, 2021

🧾 Why was it deprecated?
This policy granted broad IAM permissions, specifically:

iam:PassRole with "Resource": "*"

This meant users could pass any role to ECS tasks, creating a potential security risk.

✅ Replacement
AWS introduced a more secure replacement:
👉 AmazonECS_FullAccess

This policy scoped down IAM permissions using conditions to limit which roles can be passed.

🔁 Recommended Action
Attach the new AmazonECS_FullAccess policy to IAM roles or users currently using the old one.

Detach AmazonEC2ContainerServiceFullAccess .

After detaching, you cannot reattach the deprecated policy.

🔍 How to Detect It
In the IAM console, the deprecated policy:

Appears with a warning icon ⚠️ next to its name.

Remains active for existing identities but cannot be attached to new ones.
