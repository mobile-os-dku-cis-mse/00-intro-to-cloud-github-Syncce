# commands.md

# Assignment 1 Commands – S3 Static Website Hosting

## Environment
- **Bucket name:** `northstar-project-files-436114196583`
- **Region:** `us-east-1`
- **Website endpoint:** `http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com`

---

## Step 1: Initial mistake – wrong bucket name

### Command
```bash
aws s3api get-bucket-website --bucket orthstar-project-files-436114196583
```

### Output
```text
An error occurred (NoSuchBucket) when calling the GetBucketWebsite operation: The specified bucket does not exist
```

### Interpretation
This error occurred because the bucket name was typed incorrectly (`orthstar...` instead of `northstar...`). The command was rerun with the correct bucket name.

---

## Step 2: Verify static website hosting

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
This output confirms that static website hosting was enabled and that `index.html` was configured as the index document. At this stage, `error.html` was not yet shown in the output, so the website configuration still needed to be completed.

---

## Step 3: Verify bucket policy

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
This confirms that the bucket policy is applied and allows public read access using only `s3:GetObject`. This is the correct least-privilege permission for an S3 static website.

---

## Step 4: Verify Block Public Access configuration

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
This output confirms that all four Block Public Access settings are disabled at the bucket level. This is required so that the public-read bucket policy can work for static website hosting.

---

## Step 5: Verify uploaded objects

### Command
```bash
aws s3api list-object-versions --bucket northstar-project-files-436114196583
```

### Output
```json
{
    "Versions": [
        {
            "ETag": "\"d4ec752929fe138d4071c721ae69b479\"",
            "Size": 1286,
            "StorageClass": "STANDARD",
            "Key": "error.html",
            "VersionId": "_BvPCGyC.3sYHk55sxhcb.xOSA0_UvxH",
            "IsLatest": true,
            "LastModified": "2026-03-29T08:42:18+00:00",
            "Owner": {
                "ID": "b33a6e7725371c7b5d083f5b72242b40a252fd721aa727ae1d9ed06687e9239a"
            }
        },
        {
            "ETag": "\"b72724a9955845e0fdbc276c3d9a5052\"",
            "Size": 1318,
            "StorageClass": "STANDARD",
            "Key": "indext.html",
            "VersionId": "cT_j6WSoDu6FQ26JhJbmfwbbWdOSSBfh",
            "IsLatest": true,
            "LastModified": "2026-03-29T08:42:19+00:00",
            "Owner": {
                "ID": "b33a6e7725371c7b5d083f5b72242b40a252fd721aa727ae1d9ed06687e9239a"
            }
        },
        {
            "ETag": "\"3a2ba8b8f2222a14238d338155bfe0c0\"",
            "Size": 9723,
            "StorageClass": "STANDARD",
            "Key": "keylog.txt",
            "VersionId": "null",
            "IsLatest": false,
            "LastModified": "2026-03-18T04:40:15+00:00",
            "Owner": {
                "ID": "b33a6e7725371c7b5d083f5b72242b40a252fd721aa727ae1d9ed06687e9239a"
            }
        }
    ],
    "DeleteMarkers": [
        {
            "Owner": {
                "ID": "b33a6e7725371c7b5d083f5b72242b40a252fd721aa727ae1d9ed06687e9239a"
            },
            "Key": "keylog.txt",
            "VersionId": "SEW_aByLF0zEzuDchPIzBS4CJKdYGXod",
            "IsLatest": true,
            "LastModified": "2026-03-29T08:41:58+00:00"
        }
    ]
}
```

### Interpretation
This output shows that `error.html` had been uploaded, but the main page file was incorrectly named `indext.html` instead of `index.html`. That typo caused the website endpoint to fail when looking for the default root document.

---

## Step 6: Initial website endpoint test using placeholder (failed)

### Command
```bash
curl -I http://northstar-project-files-436114196583.s3-website-REGION.amazonaws.com
```

### Output
```text
curl: (6) Could not resolve host: northstar-project-files-436114196583.s3-website-REGION.amazonaws.com
```

### Interpretation
This failed because `REGION` was left as a placeholder. For this bucket, the correct region is `us-east-1`, so the endpoint had to be rewritten using the actual region name.

---

## Step 7: Confirm bucket region

### Command
```bash
aws s3api get-bucket-location --bucket northstar-project-files-436114196583
```

### Output
```json
{
    "LocationConstraint": null
}
```

### Interpretation
For Amazon S3, `LocationConstraint: null` means the bucket is in `us-east-1`. This confirmed the correct region needed for the website endpoint URL.

---

## Step 8: Website endpoint test before fixing index file

### Command
```bash
curl -I http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com
```

### Output
```text
HTTP/1.1 404 Not Found
x-amz-error-code: NoSuchKey
x-amz-error-message: The specified key does not exist.
x-amz-error-detail-Key: index.html
x-amz-request-id: 1VGA7WBPGT4YGQ6M
x-amz-id-2: Me2R/di8xA9ytSxKY9wSeaK5IzWSfYIhecXbb5xxET1Xj37eAUGdn+/lSBjZTccjtG4ZRGG6Jao=
Transfer-Encoding: chunked
Date: Sun, 29 Mar 2026 08:54:18 GMT
Server: AmazonS3
```

### Interpretation
This output proved that the website endpoint itself was correct, but the object `index.html` did not exist in the bucket at that moment. The root cause was the incorrect object name `indext.html`.

---

## Step 9: Final website endpoint verification

### Command
```bash
curl -I http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com
```

### Output
```text
HTTP/1.1 200 OK
x-amz-id-2: mHh0gLCY9Q6Unh42G+yoSU+paRHbL90EGcRYxEoELo12nXR9aZO4cDS20BOku7Rifl5bWldlLKU=
x-amz-request-id: F0VNXGDYAJGBDA4C
Date: Sun, 29 Mar 2026 08:55:46 GMT
Last-Modified: Sun, 29 Mar 2026 08:55:03 GMT
x-amz-version-id: _tXUZfMNvZrUrTu8Wsc_4tBN9ACBymzs
ETag: "b72724a9955845e0fdbc276c3d9a5052"
Content-Type: text/html
Content-Length: 1318
Server: AmazonS3
```

### Interpretation
This confirms that the S3 static website endpoint is reachable and returns `HTTP/1.1 200 OK`. The website is publicly accessible and the index document is now being served correctly.

---

## Step 10: Final required checks to add before submission

> Run these commands once more and replace this section with the real output before submitting.

### A. Confirm both index and error documents are configured
```bash
aws s3api get-bucket-website --bucket northstar-project-files-436114196583
```

**Expected interpretation:**  
The output should show both:
- `IndexDocument` with `index.html`
- `ErrorDocument` with `error.html`

### B. Confirm the correct objects are present after the fix
```bash
aws s3api list-object-versions --bucket northstar-project-files-436114196583
```

**Expected interpretation:**  
The output should show `index.html` and `error.html` in the bucket.

### C. Test the error document with a non-existent path
```bash
curl -I http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com/does-not-exist
```

**Expected interpretation:**  
This test should confirm that the custom error handling works for invalid paths. Capture the output and describe what it proves.

---

## Browser Validation

Open the following URL in a browser and take a screenshot with the full URL visible in the address bar:

```text
http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com
```

This screenshot is required as part of the final evidence for the assignment.

---

# EC2 + Flask Project Commands

## Grading Context
- **Attendance** — Engagement and on-time presence
- **Assignments** — Reports + commands files
- **Midterm** — Concepts + short practical
- **Final** — Design + implement basics

---

## 1. Connect to EC2

### From your Mac
```bash
ssh -i "/Users/mischa/Downloads/my-key.pem" ec2-user@3.91.7.147
```

### Upload a project folder from Mac to EC2
```bash
scp -i "/Users/mischa/Downloads/my-key.pem" -r "/Users/mischa/Downloads/Mein_Projekt_Blueprint" ec2-user@54.160.216.24:/home/ec2-user/
```

### Upload redesign ZIP from Mac to EC2
```bash
scp -i "/Users/mischa/Downloads/my-key.pem" "/Users/mischa/Downloads/northstar_redesign_v2.zip" ec2-user@3.91.7.147:/home/ec2-user/
```

---

## 2. Basic Linux Navigation on EC2

```bash
cd /home/ec2-user
ls
pwd
cd /home/ec2-user/Mein_Projekt_Blueprint
ls -la
find . -maxdepth 2 -type f | head -50
```

---

## 3. Install Required Packages on Amazon Linux 2023

### NORMAL
```bash
sudo dnf upgrade -y
sudo dnf install -y python3 python3-pip mariadb105-server httpd unzip
```

---

## 4. MariaDB / MySQL Setup

### Start MariaDB
```bash
sudo systemctl enable --now mariadb
```

### Open MariaDB shell
```bash
sudo mysql
```

### Create database and user
```sql
CREATE DATABASE meinprojekt CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'meinuser'@'localhost' IDENTIFIED BY 'Password123!';
GRANT ALL PRIVILEGES ON meinprojekt.* TO 'meinuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Import SQL file
```bash
mysql -u meinuser -p meinprojekt < mysql_setup.sql
```

### Show tables
```bash
mysql -u meinuser -p -e "SHOW TABLES;" meinprojekt
```

### Open database interactively
```bash
mysql -u meinuser -p meinprojekt
```

### Useful SQL inside MariaDB
```sql
SHOW TABLES;
DESCRIBE posts;
SELECT * FROM posts;
SELECT * FROM posts LIMIT 10;
EXIT;
```

---

## 5. Python Virtual Environment

### Create virtual environment
```bash
python3 -m venv .venv
```

### Activate virtual environment
```bash
source .venv/bin/activate
```

### Install dependencies
```bash
pip install -r requirements.txt
pip install Flask-Session
pip install gunicorn
```

### If needed
```bash
python3 -m pip install -r requirements.txt
```

---

## 6. Run Flask Locally on EC2

### VENV
```bash
python3 run.py
```

### Test in browser
```text
http://3.91.7.147:5000
```

### Stop Flask dev server
```bash
Ctrl + C
```

---

## 7. Run Flask with Gunicorn

### Run Gunicorn manually
```bash
~/.local/bin/gunicorn --bind 127.0.0.1:8000 run:app
```

### Test internally on EC2
```bash
curl http://127.0.0.1:8000
curl http://127.0.0.1:8000 | head -40
```

### Stop Gunicorn
```bash
Ctrl + C
```

---

## 8. Apache Reverse Proxy Setup

### Disable default Apache welcome page
```bash
sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf.bak
```

### Create Apache config for Flask app
```bash
sudo tee /etc/httpd/conf.d/flaskapp.conf > /dev/null <<'EOF'
<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/

    ErrorLog /var/log/httpd/flaskapp_error.log
    CustomLog /var/log/httpd/flaskapp_access.log combined
</VirtualHost>
EOF
```

### Check and restart Apache
```bash
sudo apachectl configtest
sudo systemctl restart httpd
sudo systemctl status httpd
```

---

## 9. Create systemd Service for Flask

### Create service file
```bash
sudo tee /etc/systemd/system/flaskapp.service > /dev/null <<'EOF'
[Unit]
Description=Gunicorn for Flask app
After=network.target

[Service]
User=ec2-user
Group=ec2-user
WorkingDirectory=/home/ec2-user/Mein_Projekt_Blueprint
EnvironmentFile=/home/ec2-user/Mein_Projekt_Blueprint/.env
ExecStart=/home/ec2-user/.local/bin/gunicorn --bind 127.0.0.1:8000 run:app
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```

### Reload and start service
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now flaskapp
sudo systemctl restart flaskapp
sudo systemctl status flaskapp
sudo systemctl cat flaskapp
sudo journalctl -u flaskapp -n 50 --no-pager
```

---

## 10. Check Project Files

### Read important files
```bash
cat .env
sed -n '1,80p' /home/ec2-user/Mein_Projekt_Blueprint/run.py
sed -n '1,120p' /home/ec2-user/Mein_Projekt_Blueprint/requirements.txt
sed -n '1,60p' /home/ec2-user/Mein_Projekt_Blueprint/myapp/templates/index.html
```

### Search for text in files
```bash
grep -R "Northstar" /home/ec2-user/Mein_Projekt_Blueprint/myapp/templates /home/ec2-user/Mein_Projekt_Blueprint/myapp/static
```

---

## 11. Redesign ZIP Handling

### Unzip redesign on EC2
```bash
cd /home/ec2-user
mkdir -p northstar_redesign_v2
unzip -o northstar_redesign_v2.zip -d northstar_redesign_v2
ls -R northstar_redesign_v2
```

### Copy redesign files into Flask project
```bash
cp /home/ec2-user/northstar_redesign_v2/northstar_redesign_v2/templates/*.html /home/ec2-user/Mein_Projekt_Blueprint/myapp/templates/
cp /home/ec2-user/northstar_redesign_v2/northstar_redesign_v2/static/style.css /home/ec2-user/Mein_Projekt_Blueprint/myapp/static/
cp /home/ec2-user/northstar_redesign_v2/northstar_redesign_v2/routes.py /home/ec2-user/Mein_Projekt_Blueprint/myapp/routes.py
```

### Restart after design update
```bash
sudo systemctl restart flaskapp
sudo systemctl restart httpd
```

---

## 12. Backup Existing Templates Before Replacing

```bash
cd /home/ec2-user/Mein_Projekt_Blueprint
mkdir -p backup_before_northstar/templates
mkdir -p backup_before_northstar/static

cp myapp/templates/base.html backup_before_northstar/templates/
cp myapp/templates/index.html backup_before_northstar/templates/
cp myapp/templates/posts.html backup_before_northstar/templates/
cp myapp/templates/post_detail.html backup_before_northstar/templates/
cp myapp/templates/login.html backup_before_northstar/templates/
cp myapp/templates/register.html backup_before_northstar/templates/
cp myapp/templates/create_post.html backup_before_northstar/templates/
cp myapp/templates/edit_post.html backup_before_northstar/templates/
cp myapp/static/style.css backup_before_northstar/static/
```

### Restore backup if needed
```bash
cd /home/ec2-user/Mein_Projekt_Blueprint

cp backup_before_northstar/templates/base.html myapp/templates/
cp backup_before_northstar/templates/index.html myapp/templates/
cp backup_before_northstar/templates/posts.html myapp/templates/
cp backup_before_northstar/templates/post_detail.html myapp/templates/
cp backup_before_northstar/templates/login.html myapp/templates/
cp backup_before_northstar/templates/register.html myapp/templates/
cp backup_before_northstar/templates/create_post.html myapp/templates/
cp backup_before_northstar/templates/edit_post.html myapp/templates/
cp backup_before_northstar/static/style.css myapp/static/

sudo systemctl restart flaskapp
```

---

## 13. Browser Test URLs

```text
http://3.91.7.147
http://3.91.7.147:5000
http://3.91.7.147/static/style.css
http://127.0.0.1:5000
http://127.0.0.1:8000
```

---

## 14. Helpful Notes

- **VENV commands**: Python-related commands like `pip`, `python3 run.py`, `gunicorn`
- **NORMAL commands**: system-level commands like `systemctl`, `dnf`, `mysql`, `cp`, `unzip`
- After changing **HTML/CSS**, usually:
```bash
sudo systemctl restart flaskapp
```
- After changing **Apache config**, use:
```bash
sudo apachectl configtest
sudo systemctl restart httpd
```
- If the browser still shows old content, do a hard refresh:
  - **Mac**: `Cmd + Shift + R`

---

## 15. Main Project Paths

```text
/home/ec2-user/Mein_Projekt_Blueprint
/home/ec2-user/Mein_Projekt_Blueprint/myapp
/home/ec2-user/Mein_Projekt_Blueprint/myapp/templates
/home/ec2-user/Mein_Projekt_Blueprint/myapp/static
/home/ec2-user/Mein_Projekt_Blueprint/.env
/home/ec2-user/Mein_Projekt_Blueprint/run.py
```
