---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "AWS S3 Signed Upload URL Basics"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- AWS
- S3
categories: []
date: 2020-03-19T17:32:25-04:00
lastmod: 2020-03-19T17:32:25-04:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

While I found a number of examples for generating signed upload S3 URLs, there didn't seem to be examples with the basics.

After substituting the name for your bucket, file name and expiry desired, run below code to generate the URL:

```
import boto3

if __name__ == "__main__":
    s3_client = boto3.client('s3')
    response = s3_client.generate_presigned_url(
        ClientMethod='put_object',
        Params={"Bucket": "mybucket", "Key": "file.pdf"},
        ExpiresIn=48*60*60,
        HttpMethod="PUT")
    print(response)
```

To upload from command line run below (substituting URL from previous section):

```
curl "https://mybucket.s3.amazonaws.com/file.pdf?AWSAccessKeyId=AKIAWR6H3WAK6NZTVBJP&Signature=CkFT8z3KK8zznNSZ6sOZryFHQTM%3D&Expires=1584826340" --upload-file file.pdf
```