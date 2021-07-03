---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "AWS CloudFormation Starter/`null` Template"
subtitle: ""
summary: ""
authors: []
tags:
- AWS
- CloudFormation
categories: []
date: 2021-07-03T16:50:49Z
lastmod: 2021-07-03T16:50:49Z
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

If the CloudFormation create has an error, the CloudFormation doesn't roll back so it has to be deleted and recreated. To avoid multiple initial create/delete, the best approach is to use a `null` CloudFormation. An example is below:

```
AWSTemplateFormatVersion: 2010-09-09
Description: CloudFormation Starter

Conditions:
  Never: !Equals [ a, b ]

Resources:
  NullResource:
    Type: Custom::Null
    Condition: Never
```
