# report.md

# Assignment 1 Report – S3 Static Website Hosting

## 1. Objective

The objective of this assignment was to deploy a static website using Amazon S3, make it publicly accessible, and verify the configuration using AWS CLI commands. The assignment required correct static website hosting configuration, proper public access settings, a least-privilege bucket policy, and clear technical documentation.

## 2. Architecture Overview

The solution uses a single Amazon S3 bucket configured for static website hosting.

### Components
- **Bucket name:** `northstar-project-files-436114196583`
- **AWS Region:** `us-east-1` (US East / N. Virginia)
- **Index document:** `index.html`
- **Error document:** `error.html`
- **Bucket policy:** public read access using only `s3:GetObject`
- **Website endpoint:** `http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com`

### Request Flow
1. HTML files are uploaded to the S3 bucket.
2. Static website hosting is enabled on the bucket.
3. Block Public Access settings are disabled at bucket level so a public website policy can be used.
4. A least-privilege bucket policy allows anonymous read access to objects.
5. The website is accessed through the S3 **website endpoint**, not the REST endpoint.

## 3. Implementation Process

### Step 1: Bucket Selection and Region Verification
I worked with the bucket `northstar-project-files-436114196583` in the `us-east-1` region. When I checked the bucket location with the AWS CLI, the command returned `LocationConstraint: null`, which is the expected AWS behavior for `us-east-1`.

### Step 2: Static Website Hosting
I enabled static website hosting on the bucket and configured:
- `index.html` as the index document
- `error.html` as the error document

### Step 3: Public Access Configuration
I verified that all four Block Public Access (BPA) settings were disabled:
- `BlockPublicAcls = false`
- `IgnorePublicAcls = false`
- `BlockPublicPolicy = false`
- `RestrictPublicBuckets = false`

This step was necessary because public static website hosting requires the bucket to allow a public-read policy.

### Step 4: Bucket Policy
I applied a least-privilege bucket policy that allows public access only to read website objects:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::northstar-project-files-436114196583/*"
    }
  ]
}
```

This policy gives anonymous users only the minimum permission required to load the website.

### Step 5: File Upload and Validation
I uploaded the website files to the bucket and tested the website endpoint. The final HTTP validation returned `HTTP/1.1 200 OK`, confirming that the static site was reachable through the correct S3 website endpoint.

## 4. Security Decisions

The security configuration follows the principle of least privilege.

### Least-Privilege Access
The bucket policy grants only:
- `s3:GetObject`

It does not grant:
- `s3:*`
- `s3:PutObject`
- `s3:DeleteObject`

This is the minimum permission needed for a public static website.

### Public Access Trade-off
A public website requires public object reads, so Block Public Access settings had to be disabled at bucket level. This introduces a controlled risk, but the risk is reduced because access is limited strictly to reading objects and not modifying or deleting them.

## 5. Verification

The deployment was verified using the required AWS CLI commands:
1. `aws s3api get-bucket-website --bucket northstar-project-files-436114196583`
2. `aws s3api get-bucket-policy --bucket northstar-project-files-436114196583`
3. `aws s3api get-public-access-block --bucket northstar-project-files-436114196583`
4. `aws s3api list-object-versions --bucket northstar-project-files-436114196583`

I also tested the public website endpoint with:

```bash
curl -I http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com
```

The response returned `HTTP/1.1 200 OK`, which confirms that the website is publicly accessible and that the index document is being served correctly.

## 6. Troubleshooting

During setup and verification, I encountered several issues:

### Mistyped Bucket Name
At first, I ran a command using a misspelled bucket name (`orthstar-project-files-436114196583`), which returned `NoSuchBucket`. This was corrected by rerunning the command with the correct bucket name.

### Wrong Placeholder in Website URL
I initially tested the URL with `REGION` left as a placeholder instead of replacing it with the actual region. This caused the host lookup to fail. After replacing it with `us-east-1`, the endpoint format was correct.

### Missing `index.html`
The first website test returned `404 Not Found` with `NoSuchKey` for `index.html`. This showed that the website endpoint was correct but the required index file was missing or named incorrectly. After uploading the correct `index.html`, the website returned `200 OK`.

### File Naming Issue
The bucket output showed that one object had previously been uploaded as `indext.html` instead of `index.html`. This typo prevented S3 static website hosting from locating the root document correctly.

## 7. Conclusion

This assignment demonstrated how to configure Amazon S3 for public static website hosting, apply a least-privilege bucket policy, and verify the configuration using AWS CLI commands and browser-style HTTP testing. The final result was a publicly accessible static website hosted from the S3 website endpoint in `us-east-1`.
