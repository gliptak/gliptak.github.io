
---
title: Running Jupyterhub Docker
authors:
- gliptak
tags:
- Python
- Jupyter
categories: []
date: "2020-12-19T17:07:05-04:00"
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

`Jupyterhub` doesn't seem to have complete instructions available on simply running an instance under Docker with a number of issues, Stackoverflow questions asking for clarifications. These are my quick notes:

As the Docker image name indicates, this is [singleuser](https://hub.docker.com/r/jupyterhub/singleuser) instance preconfigured for user `jovyan`. Run below command in directory with your notebooks

```
docker run --rm -p 8000:8000 -d -v `pwd`:/home/jovyan/work --name jupyterhub jupyterhub/singleuser jupyterhub
```

There doesn't seem to be (default) password set for `jovyan` user, so at this point you cannot login into the GUI. Set password for `jovyan`

```
docker exec --user root -it jupyterhub bash
# passwd jovyan
New password: 
Retype new password: 
passwd: password updated successfully
# exit
exit
```

> ℹ️ Above shell command can also be used to install system packages and pip install Python libraries. Please note that additional installs will not persist beyond stopping this current instance

After completing above steps, login to `Jupyterhub` at [http://localhost:8000/user/jovyan/tree/work](http://localhost:8000/user/jovyan/tree/work)

To cleanup running instance

```
docker stop jupyterhub
```