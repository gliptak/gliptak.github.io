---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "AWS Lambda Lookup AccountId and Region"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- AWS
- Lambda
categories: []
date: 2020-10-02T18:32:25-04:00
lastmod: 2020-10-02T18:32:25-04:00
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

For a number of uses including generating IAM roles and various policies, lambda code might require access to current account id and region. Here is a code snippet on how to acquire those values:

```
import json
import logging
import boto3

logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    logger.info(json.dumps(event))
    account_id = boto3.client('sts').get_caller_identity().get('Account')
    # use a client which is region based
    logs_client = boto3.client('logs')
    region_name = logs_client.meta.region_name
    logger.info(f"{account_id}:{region_name}")
```