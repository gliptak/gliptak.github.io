---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Github Create Pull Request with GraphQL"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- Github
- GraphQL
categories: []
date: 2021-12-12T20:32:51Z
lastmod: 2021-12-12T20:32:51Z
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

GraphQL API allows for query and mutation (update) operations which can be executed in [GitHub's GraphQL Explorer](https://docs.github.com/en/graphql/overview/explorer). Below example creates a PR. First, find the repository id:

```
{
  repository(owner: "gliptak", name: "githubapitest1") {
    id
  }
}
```

The repository id returned to be used to create the pull request:

```json
{
  "data": {
    "repository": {
      "id": "R_kgDOGS1otA"
    }
  }
}
```

The mutation (update) creates a new PR from an existing branch (additional details could have been submitted):

```
mutation {
  createPullRequest(input: {
    repositoryId: "R_kgDOGS1otA"
    title: "my PR",
    baseRefName: "master",
    headRefName: "patch-1"}) {
    pullRequest {
      url
    }
  }
}
```

Resulting JSON displays the URL of PR created:

```json
{
  "data": {
    "createPullRequest": {
      "pullRequest": {
        "url": "https://github.com/gliptak/githubapitest1/pull/1"
      }
    }
  }
}
```

At the time of publishing, [@export directive](https://github.com/graphql/graphql-spec/issues/377) allowing to `chain` variables between queries is under consideration so variable substitution has to happen manually or programmatically.