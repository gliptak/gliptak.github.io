---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "AWS Cloudformation Referencing AMIs Using SSM Parameter Store"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- AWS
- Cloudformation
categories: []
date: 2019-09-11T21:32:25-04:00
lastmod: 2019-09-11T21:32:25-04:00
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

When AWS infrastructure configured in "traditional" compute/storage/network style, identifying, referencing and patching AMIs in all regions in use is crucial. Cloudformation has a way to redirect AMI references through SSM Parameter Store.

This represents a tradeoff, as recreating the Cloudformation stack might pickup the next (patched) AMI hence it is no longer immutable. But resulting state is similar to externally patched Linux/Windows images which also cannot be recreated by simply redeploying Cloudformation.

Th Cloudformation syntax as follows. ```AmiId``` can point to both AWS and customer published values:

```
AWSTemplateFormatVersion: '2010-09-09'

Parameters:
  AmiId:
    Type: AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>
    Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-ebs

Resources:
  Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !Ref AmiId
```

At the time of this posting, here are some AWS maintained AMIs:

```
aws ssm get-parameters-by-path --path /aws/service/ami-amazon-linux-latest | jq ".Parameters[].Name"
"/aws/service/ami-amazon-linux-latest/amzn-ami-hvm-x86_64-ebs"
"/aws/service/ami-amazon-linux-latest/amzn-ami-hvm-x86_64-gp2"
"/aws/service/ami-amazon-linux-latest/amzn-ami-hvm-x86_64-s3"
"/aws/service/ami-amazon-linux-latest/amzn-ami-minimal-hvm-x86_64-s3"
"/aws/service/ami-amazon-linux-latest/amzn-ami-minimal-pv-x86_64-s3"
"/aws/service/ami-amazon-linux-latest/amzn-ami-pv-x86_64-s3"
"/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-arm64-gp2"
"/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-ebs"
"/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
"/aws/service/ami-amazon-linux-latest/amzn2-ami-minimal-hvm-arm64-ebs"
"/aws/service/ami-amazon-linux-latest/amzn-ami-minimal-hvm-x86_64-ebs"
"/aws/service/ami-amazon-linux-latest/amzn-ami-minimal-pv-x86_64-ebs"
"/aws/service/ami-amazon-linux-latest/amzn-ami-pv-x86_64-ebs"
"/aws/service/ami-amazon-linux-latest/amzn2-ami-minimal-hvm-x86_64-ebs"
```

```
aws ssm get-parameters-by-path --path /aws/service/ami-windows-latest | jq ".Parameters[].Name" | grep "English" | grep "\-2019"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-SQL_2017_Web"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-SQL_2016_SP2_Standard"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-SQL_2016_SP2_Web"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-HyperV"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Core-Base"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Core-ContainersLatest"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-SQL_2017_Express"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-SQL_2016_SP2_Express"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-Base"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-SQL_2017_Standard"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-ContainersLatest"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-SQL_2017_Enterprise"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-SQL_2016_SP2_Enterprise"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Tesla"
"/aws/service/ami-windows-latest/Windows_Server-2019-English-Full-ECS_Optimized"
```

And some "oddballs"

```
aws ssm get-parameters-by-path --path /aws/service/ami-windows-latest | jq ".Parameters[].Name" | grep -v Windows
"/aws/service/ami-windows-latest/amzn2-ami-hvm-2.0.20190313-x86_64-gp2-SQL_2017_Express"
"/aws/service/ami-windows-latest/RHEL-7.6_HVM_GA-20181017-x86_64-0-Hourly2-GP2-SQL_2017_Enterprise"
"/aws/service/ami-windows-latest/ubuntu-bionic-18.04-amd64-server-20180522-dotnetcore"
"/aws/service/ami-windows-latest/amzn2-ami-hvm-2.0.20180622.1-x86_64-gp2-dotnetcore"
"/aws/service/ami-windows-latest/ubuntu-xenial-16.04-amd64-server-20190212-SQL_2017_Web"
"/aws/service/ami-windows-latest/ubuntu-xenial-16.04-amd64-server-20180306-SQL_2017_Express"
"/aws/service/ami-windows-latest/ubuntu-xenial-16.04-amd64-server-20180306-SQL_2017_Standard"
"/aws/service/ami-windows-latest/ubuntu-xenial-16.04-amd64-server-20180306-SQL_2017_Enterprise"
"/aws/service/ami-windows-latest/ubuntu-xenial-16.04-amd64-server-20180306-SQL_2017_Web"
"/aws/service/ami-windows-latest/amzn2-ami-hvm-2.0.20190313-x86_64-gp2-SQL_2017_Enterprise"
"/aws/service/ami-windows-latest/ubuntu-xenial-16.04-amd64-server-20190212-SQL_2017_Enterprise"
"/aws/service/ami-windows-latest/amzn2-ami-hvm-2.0.20190313-x86_64-gp2-SQL_2017_Web"
"/aws/service/ami-windows-latest/ubuntu-xenial-16.04-amd64-server-20190212-SQL_2017_Express"
"/aws/service/ami-windows-latest/amzn2-ami-hvm-2.0.20190313-x86_64-gp2-SQL_2017_Standard"
"/aws/service/ami-windows-latest/ubuntu-xenial-16.04-amd64-server-20190212-SQL_2017_Standard"
```