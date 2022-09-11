---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Github List Repositories Paginated with GraphQL"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- Github
- GraphQL
categories: []
date: 2022-09-11T02:42:42Z
lastmod: 2022-09-11T02:42:42Z
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

One of the Github GraphQL operations supporting pagination is searching/listing repositories. Below queries utilize `query variables` running in [GitHub's GraphQL Explorer](https://docs.github.com/en/graphql/overview/explorer).

```json
{
  "query": "org:gliptak is:all",
  "pageSzie": 2,
  "after": ""
}
```

Below setup has 2 for pagination size for demonstration purposes, larger window to be used in real queries. The initial query:

```
query ListRepos($query: String!, $pageSize: Int!) {
  search(query: $query, type: REPOSITORY, first: $pageSize) {
    edges {
      node {
        ... on Repository {
          name
          defaultBranchRef {
            name
          }
        }
      }
    }
    pageInfo {
      startCursor
      endCursor
      hasNextPage
    }
  }
}
```

returns repo details and cursor values:

```json
{
  "data": {
    "search": {
      "edges": [
        {
          "node": {
            "name": "JAllele",
            "defaultBranchRef": {
              "name": "master"
            }
          }
        },
        {
          "node": {
            "name": "aws-cdk-python",
            "defaultBranchRef": {
              "name": "master"
            }
          }
        }
      ],
      "pageInfo": {
        "startCursor": "Y3Vyc29yOjE=",
        "endCursor": "Y3Vyc29yOjI=",
        "hasNextPage": true
      }
    }
  }
}
```

Which can be used to form the next query:

```
query ListRepos($query: String!, $pageSize: Int!, $after: String!) {
  search(query: $query, type: REPOSITORY, after: $after, first: $pageSize) {
    edges {
      node {
        ... on Repository {
          name
          defaultBranchRef {
            name
          }
        }
      }
    }
    pageInfo {
      startCursor
      endCursor
      hasNextPage
    }
  }
}
```

Above will return similar JSON and to be called with next `after` cursor value while `hasNextPage` is `true`.