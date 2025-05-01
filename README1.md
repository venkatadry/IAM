I have given this in Bucket policy

```{
    "Version":"2012-10-17",
    "Statement": [
        {
            "Sid":"GrantAnonymousReadPermissions",
            "Effect":"Allow",
            "Principal": "arn:aws:iam::92xxxxxx:user/test-user",
            "Action":["s3:GetObject"],
            "Resource":["arn:aws:s3:::xyzkvp/*"]
        }
    ]
}
```
It is saying syntax error

❌ 1. Incorrect usage of Principal for a specific IAM user in a bucket policy
Issue: You are using a specific IAM user's ARN ("Principal": "arn:aws:iam::920373005946:user/test-user") in a bucket policy.

Explanation: In S3 bucket policies, Principal must be either:

"*" (for anonymous/public access), or

An AWS account ID or an AWS block like:

json
Copy
Edit
"Principal": {
  "AWS": "arn:aws:iam::920373005946:user/test-user"
}
However, granting access to a specific IAM user of another account in a bucket policy is not recommended or typically supported. You should grant access at the bucket policy level to the entire AWS account, and then that account can manage user-level permissions internally.


✅ Recommended Fix:
If you're granting access to a user in your own account:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GrantAccountReadPermissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::920373005946:user/test-user"
      },
      "Action": ["s3:GetObject"],
      "Resource": ["arn:aws:s3:::xyzkvp/*"]
    }
  ]
}
 ```
