---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Github List Files Recursively with GraphQL"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- Github
- GraphQL
categories: []
date: 2022-09-11T16:56:52Z
lastmod: 2022-09-11T16:56:52Z
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

At this point GraphQL doesn't seem to provide the right constructs to list files folders of unconstrained depth. Instead, users will make multiple calls into the API. So let's get started:

```json
{
  "owner": "gliptak",
  "name": "aws-cdk-python",
  "rev": ""
}
```

```
query DefaultBranch($owner: String!, $name: String!) {
  repository(owner: $owner, name: $name) {
    name
    defaultBranchRef {
      name
    }
  }
}
```

returns

```json
{
  "data": {
    "repository": {
      "name": "aws-cdk-python",
      "defaultBranchRef": {
        "name": "master"
      }
    }
  }
}
```

Subtitute the default branch from above into the query variables with `:` appended (`master:` for our example) and run:

```
query RepoFiles($owner: String!, $name: String!, $rev: String!) {
  repository(owner: $owner, name: $name) {
    object(expression: $rev) {
      id
      ... on Tree {
        entries {
          name
          oid
          type
        }
      }
    }
  }
}
```

[rev](https://git-scm.com/docs/git-rev-parse) above points to a directory `Tree` to list entries for returning (slightly abbreviated):

```json
{
  "data": {
    "repository": {
      "object": {
        "entries": [
          {
            "name": ".env",
            "oid": "9a10a235bf64f16f2684148e33c7aaf5eaa88721",
            "type": "blob"
          },
          {
            "name": ".github",
            "oid": "17ddafeaf72048e2dcb4a3ccb05ffbc12459cabd",
            "type": "tree"
          },
          ...
        ]
      }
    }
  }
}
```

From above `oid`/`path`(including branch prefix) can be used to list entries of `tree`s and to display content of `blob`s as below:

```
query RepoFiles($owner: String!, $name: String!, $rev: String!) {
  repository(owner: $owner, name: $name) {
    object(expression: $rev) {
      ... on Blob {
        text
      }
    }
  }
}
```

```json
{
  "data": {
    "repository": {
      "object": {
        "text": "USER_ID=1001\nGROUP_ID=1001\n"
      }
    }
  }
}
```

API transaction limits might apply so multiple queries could be performed in a single call.