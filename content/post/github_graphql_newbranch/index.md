---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Github Create Branch with GraphQL"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- Github
- GraphQL
categories: []
date: 2022-09-11T20:10:44Z
lastmod: 2022-09-11T20:10:44Z
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

First gather some context:

```
{
  repository(owner: "gliptak", name: "githubapitest1") {
    id
    defaultBranchRef {
      name
      target {
        oid
      }
    }
  }
}
```

Substitute `repositoryId` and `oid` from above and use the name of the to be created branch:

```json
{
  "input": {
    "clientMutationId": "1234",
    "name": "refs/heads/<BRANCH_NAME>",
    "oid": "<OID>",
    "repositoryId": "<REPOSITORY_ID"
  }
}
```

```
mutation AddBranch($input: CreateRefInput!) {
  createRef(input: $input) {
    ref {
      name
    }
    clientMutationId
  }
}
```

Success returns

```json
{
  "data": {
    "createRef": {
      "ref": {
        "name": "my_branch"
      },
      "clientMutationId": "1234"
    }
  }
}
```

And in case of error, relevant details are returned

```json
{
  "data": {
    "createRef": null
  },
  "errors": [
    {
      "type": "UNPROCESSABLE",
      "path": [
        "createRef"
      ],
      "locations": [
        {
          "line": 2,
          "column": 3
        }
      ],
      "message": "A ref named \"refs/heads/my_branch\" already exists in the repository."
    }
  ]
}
```