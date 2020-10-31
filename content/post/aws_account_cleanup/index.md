---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "AWS Account Cleanup"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- AWS
categories: []
date: 2020-10-31T10:17:30-04:00
lastmod: 2020-10-31T10:17:30-04:00
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

Many AWS customers skip initial planning steps and don't establish infrastructure-as-code scripting practices initially necessitating querying and cleaning up resources to avoid billing charges.

## AWS tooling

+ [AWS Cost Explorer](https://aws.amazon.com/aws-cost-management/aws-cost-explorer/)
+ [AWS Billing Console](https://console.aws.amazon.com/billing/)
+ [AWS Tag Editor](https://docs.aws.amazon.com/ARG/latest/userguide/tag-editor.html) select all regions, all resource types and uncheck tags

The open source community developed a number of tools

## List all AWS resources

+ [aws_list_all](https://github.com/JohannesEbke/aws_list_all) command line
+ [awsls](https://github.com/jckuester/awsls)
+ [AWSRetriever](https://github.com/dtylman/AWSRetriever) desktop
+ [aws-inventory](https://github.com/nccgroup/aws-inventory) command line and GUI

## Filtered delete

+ [aws-nuke](https://github.com/rebuy-de/aws-nuke) extensive filtering
+ [cloud-nuke](https://github.com/gruntwork-io/cloud-nuke) filtering by region, age, resourcetype is available
