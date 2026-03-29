eee_W_5881166@runweb219637:~$ aws s3api get-bucket-website --bucket northstar-project-files-436114196583
{
    "IndexDocument": {
        "Suffix": "index.html"
    }
}
eee_W_5881166@runweb219637:~$ aws s3api get-bucket-policy --bucket northstar-project-files-436114196583
{
    "Policy": "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Sid\":\"PublicReadGetObject\",\"Effect\":\"Allow\",\"Principal\":\"*\",\"Action\":\"s3:GetObject\",\"Resource\":\"arn:aws:s3:::northstar-project-files-436114196583/*\"}]}"
}
eee_W_5881166@runweb219637:~$ aws s3api get-public-access-block --bucket northstar-project-files-436114196583
{
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": false,
        "IgnorePublicAcls": false,
        "BlockPublicPolicy": false,
        "RestrictPublicBuckets": false
    }
}
eee_W_5881166@runweb219637:~$ aws s3api list-object-versions --bucket northstar-project-files-436114196583
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
            "Key": "index.html",
            "VersionId": "_tXUZfMNvZrUrTu8Wsc_4tBN9ACBymzs",
            "IsLatest": true,
            "LastModified": "2026-03-29T08:55:03+00:00",
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
            "IsLatest": false,
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
...skipping...
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
            "Key": "index.html",
            "VersionId": "_tXUZfMNvZrUrTu8Wsc_4tBN9ACBymzs",
            "IsLatest": true,
            "LastModified": "2026-03-29T08:55:03+00:00",
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
            "IsLatest": false,
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
            "Key": "indext.html",
            "VersionId": "evMZw26NKiOmPFMjTwedNLyB8ycReLmg",
            "IsLatest": true,
            "LastModified": "2026-03-29T08:55:04+00:00"
        },
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

eee_W_5881166@runweb219637:~$ curl -I http://northstar-project-files-436114196583.s3-website-us-east-1.amazonaws.com
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

eee_W_5881166@runweb219637:~$ 