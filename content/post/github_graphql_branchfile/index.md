---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Github Add/Update/Delete File(s) on Branch with GraphQL"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- Github
- GraphQL
categories: []
date: 2022-09-11T23:07:14Z
lastmod: 2022-09-11T23:07:14Z
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

In `input` contents is `base64` encoded and `expectedHeadOid` must match with last commit on branch referenced and operation will fails on mismatch:

```json
{
  "input": {
    "branch": {
      "repositoryNameWithOwner": "gliptak/githubapitest1",
      "branchName": "my_branch"
    },
    "message": {
      "headline": "Hello from GraphQL!"
    },
    "fileChanges": {
      "additions": [
        {
          "path": "graphql.txt",
          "contents": "SGVsbG8gZnJvbSBHcmFwaFFMIQo="
        }
      ],
      "deletions": [
        {
          "path": "README.md"
        }
      ]
    },
    "expectedHeadOid": "74512c01ecaec77e38497d448d391065c07ce973"
  }
}
```

Mutation below updates branch with fileChanges listed:

```
mutation ($input: CreateCommitOnBranchInput!) {
  createCommitOnBranch(input: $input) {
    commit {
      url
    }
  }
}
```

and returns:

```json
{
  "data": {
    "createCommitOnBranch": {
      "commit": {
        "oid": "dfeef335f3f622e090497d45e13ecaff5ca9470f",
        "changedFilesIfAvailable": 2
      }
    }
  }
}
```

Various other data elements can also be returned.