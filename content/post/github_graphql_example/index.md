---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Github GraphQL Example"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- Github
- GraphQL
categories: []
date: 2021-10-31T16:18:41Z
lastmod: 2021-10-31T16:18:41Z
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

While Github GraphQL offers detailed [documentation and an API Explorer](https://docs.github.com/en/graphql), it doesn't have (Python) code samples. Below is a basic [GQL](https://github.com/graphql-python/gql) example. The query can be developed/tested in [Explorer](https://docs.github.com/en/graphql/overview/explorer)

```python
from gql import gql, Client
from gql.transport.requests import RequestsHTTPTransport
import click


@click.command()
@click.option('--githubtoken', envvar="GITHUB_TOKEN", prompt=True, hide_input=True, help='Github token')
@click.option('--owner', help='Repository owner', required=True)
@click.option('--repository', help='Repository name', required=True)
def gh_query_repo(githubtoken, owner, repository):
    transport=RequestsHTTPTransport(
        url='https://api.github.com/graphql',
        use_json=True,
        headers={
            "Authorization": f"Bearer {githubtoken}",
        },
        #verify=False,
        retries=3,
    )

    client = Client(
        transport=transport,
        fetch_schema_from_transport=True,
    )

    query_repo = '''
        query Repository($owner: String!, $repo: String!) {
            repository(name: $repo, owner: $owner) {
                id
            }
        }
        ''' // [^1]
    result = client.execute(
        gql(query_repo),
        variable_values={
                "owner": owner,
                "repo": repository,
            }
        )
    repositoryId = result['repository']['id']
    print(repositoryId)


if __name__ == "__main__":
    gh_query_repo()
```
