---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Splunk custom input with session"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- Splunk
- Python
categories: []
date: 2020-10-31T11:17:30-04:00
lastmod: 2020-10-31T11:17:30-04:00
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

```Splunk``` can be extended with custom inputs written in ```Python```. In order to connect to ```Splunk``` services, code has to be configured and use a session token. Here is a basic setup emitting records with ```kvstore``` names

```
#!/usr/bin/env python

import splunklib.client as client
import sys
import datetime as dt

def generate(session_key):
    service = client.connect(token = session_key)
    for collection in service.kvstore:
        ts = dt.datetime.now(tz=dt.timezone.utc).isoformat()
        print(f'{ts}, collection="{collection.name}"')

if __name__ == "__main__":
    session_key = sys.stdin.read()
    generate(session_key)
```

and the corresponding ```default/inputs.conf```

```
[script://./bin/listkvstores.py]
interval = 300
disabled = False
passAuth = nobody
```

```kvstore``` access uses user ```nobody```, other ```Splunk``` services might need a different user configured.