# IAM
***IAM (Identity and Access Management) in AWS is global.***

This means:
IAM users, groups, roles, and policies are not tied to a specific AWS region.
They exist and are shared across all regions.
When you create or update IAM resources, it automatically applies globally.
But — the permissions you set through IAM can control access to region-specific services (like EC2 in us-east-1, eu-west-1, etc.).

![image](https://github.com/user-attachments/assets/3b116362-adc7-4000-9a72-b30d1d8e23d6)

By Default Everything is denoied for user in AWS
Account A can call resources in Account B we can use resource based policy
Aws managed policy updates will happen automatically?
if you lost access key you have to recreate it

