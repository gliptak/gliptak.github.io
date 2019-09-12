---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "AWS Organizations CLI"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- AWS
categories: []
date: 2019-09-07T21:17:30-04:00
lastmod: 2019-09-07T21:17:30-04:00
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

Many AWS customers take advantage of AWS Organizations to organize and secure their workloads. In many cases, users login into their ```master``` account and configure permissions allowing to switch to ```member``` accounts in the Console. The same permissions can be used for ```AWS CLI```.

In this example below, a single ```AWS Access Key``` has to be generated (and rotated) in the ```master``` account and it can be used to switch to ```test/2222222``` and ```production/3333333``` accounts using the ```CrossAccountAccessRole``` already configured for switching in the Console.


```
[master]
aws_access_key_id     = AKIZZZZZZZZZZZZ
aws_secret_access_key = a78594065069565

[test]
role_arn = arn:aws:iam::2222222:role/CrossAccountAccessRole
source_profile = master

[production]
role_arn = arn:aws:iam::3333333:role/CrossAccountAccessRole
source_profile = master
```