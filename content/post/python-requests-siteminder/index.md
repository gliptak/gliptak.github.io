
---
title: Login into a CA SSO/Siteminder protected site with Python Requests
authors:
- gliptak
tags:
- Python
- Requests
- Siteminder
- SSO
categories: []
date: "2017-06-02T19:07:05-04:00"
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

While this below code is simple, it uses two important approaches:

+ utilizes a Requests Session to keep Siteminder login cookies/headers
+ it has a two step load, allowing to fill out the Siteminder form


```python
import requests

if __name__ == "__main__":
    mysite = 'http://mysite/'
    credentials = {'USER': 'myuser', 'PASSWORD': 'mypassword'}
    s = requests.session() # use Session to keep cookies around
    page = s.get(mysite)
    s.post(page.url, data=credentials) # page.url is the Siteminder login screen
    page = s.get(mysite)
    print(page.content)
```
