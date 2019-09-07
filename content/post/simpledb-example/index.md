
---
title: AWS SimpleDB Boto3 Example
authors:
- gliptak
tags:
- aws
- boto3
categories: []
date: "2017-02-15T17:51:55-05:00"
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
image:
  caption: ""
  focal_point: ""

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references 
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

When looking into AWS SimpleDB, a quick search didn't return any short Python Boto3 examples. So I decided to post one.
(As with any services you to subscribe to, running this code below might cost you money ...)

```python
from __future__ import print_function
import boto3


def quote(string):
    return string.replace("'", "''").replace('"', '""').replace('`', '``')


def put_attributes(sdb, domain, id, color):
    response = sdb.put_attributes(
        DomainName=domain,
        ItemName=id,
        Attributes=[
            {
                'Name': 'color',
                'Value': color,
                'Replace': True
            },
        ],
    )
    print(response)


if __name__ == "__main__":
    domain = "TEST_DOMAIN"
    sdb = boto3.client('sdb')
    response = sdb.create_domain(
        DomainName=domain
    )
    print(response)
    response = sdb.list_domains(
    )
    print("Current domains: %s" % response['DomainNames'])
    put_attributes(sdb, domain, "id1", "red")
    put_attributes(sdb, domain, "id2", "redblue")
    put_attributes(sdb, domain, "id3", "blue")
    response = sdb.select(
        SelectExpression='select * from %s where color like "%%%s%%"' % (domain, quote('blue')),
    )
    print(response)
    response = sdb.delete_domain(
        DomainName=domain
    )
    print(response)
```
