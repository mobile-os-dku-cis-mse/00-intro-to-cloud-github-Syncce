# Verification

## S3 Static Website Hosting Verification

### Environment
- **Bucket Name:** `northstar-project-files-436114196583`
- **Region:** `us-east-1`
- **Website Endpoint:** `http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com`

---

## 1. Verify Website Configuration

### Command
```bash
aws s3api get-bucket-website --bucket northstar-project-files-436114196583
```

### Output
```json
{
  "IndexDocument": {
    "Suffix": "index.html"
  }
}
```

### Interpretation
This confirms that static website hosting is enabled and that `index.html` is configured as the index document.

---

## 2. Verify Bucket Policy

### Command
```bash
aws s3api get-bucket-policy --bucket northstar-project-files-436114196583
```

### Output
```json
{
  "Policy": "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Sid\":\"PublicReadGetObject\",\"Effect\":\"Allow\",\"Principal\":\"*\",\"Action\":\"s3:GetObject\",\"Resource\":\"arn:aws:s3:::northstar-project-files-436114196583/*\"}]}"
}
```

### Interpretation
This confirms that the bucket policy allows public read access using only `s3:GetObject`, which is the correct least-privilege permission for an S3 static website.

---

## 3. Verify Block Public Access Settings

### Command
```bash
aws s3api get-public-access-block --bucket northstar-project-files-436114196583
```

### Output
```json
{
  "PublicAccessBlockConfiguration": {
    "BlockPublicAcls": false,
    "IgnorePublicAcls": false,
    "BlockPublicPolicy": false,
    "RestrictPublicBuckets": false
  }
}
```

### Interpretation
This confirms that all four Block Public Access settings are disabled at the bucket level, which is required for public static website hosting.

---

## 4. Verify Uploaded Objects

### Command
```bash
aws s3api list-object-versions --bucket northstar-project-files-436114196583
```

### Output
```json
{
  "Versions": [
    {
      "Key": "error.html",
      "IsLatest": true,
      "LastModified": "2026-03-29T08:42:18+00:00"
    },
    {
      "Key": "index.html",
      "IsLatest": true,
      "LastModified": "2026-03-29T08:55:03+00:00"
    },
    {
      "Key": "indext.html",
      "IsLatest": false,
      "LastModified": "2026-03-29T08:42:19+00:00"
    }
  ],
  "DeleteMarkers": [
    {
      "Key": "indext.html",
      "IsLatest": true,
      "LastModified": "2026-03-29T08:55:04+00:00"
    }
  ]
}
```

### Interpretation
This confirms that `index.html` and `error.html` exist in the bucket. It also shows that `indext.html` was an earlier incorrect filename and was later deleted.

---

## 5. Verify Website Endpoint Response

### Command
```bash
curl -I http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com
```

### Output
```text
HTTP/1.1 200 OK
x-amz-id-2: WPTAX56AiqPECIDjVTzeDH3uCykQ5akGAAWTGOtCrT21bMPizIMSSDNhOlA8YFxi395+RejZJzP9vdtLeuGZmEJTUK8V/1RTbgJnGFo2Pjo=
x-amz-request-id: 59YC7QKEQC6743K8
Date: Sun, 29 Mar 2026 08:57:39 GMT
Last-Modified: Sun, 29 Mar 2026 08:55:03 GMT
x-amz-version-id: _tXUZfMNvZrUrTu8Wsc_4tBN9ACBymzs
ETag: "b72724a9955845e0fdbc276c3d9a5052"
Content-Type: text/html
Content-Length: 1318
Server: AmazonS3
```

### Interpretation
This confirms that the S3 static website endpoint is reachable and returns `HTTP/1.1 200 OK`. The website is publicly accessible and the index document is being served successfully.

---

## Browser Validation

Open this URL in a browser and take a screenshot with the full URL visible in the address bar:

```text
http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com
```
