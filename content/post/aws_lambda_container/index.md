---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Packaging AWS Lambda functions with large dependencies as containers"
subtitle: ""
summary: ""
authors:
- gliptak
tags:
- AWS
- Lambda
- Docker
categories: []
date: 2021-06-27T01:47:41-04:00
lastmod: 2021-06-27T01:47:41-04:00
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

While the actual application code for many lambda functions might be small, in many (data science) usecases libraries used and to be packaged together push over the deployment package limit of 50 MB compressed. Container packaging to the rescue.

A sample `Dockerfile` installing [SageMaker Python SDK](https://github.com/aws/sagemaker-python-sdk)

```
ARG WORK_DIR="/home/app/"

FROM public.ecr.aws/lambda/python:3.8
ARG WORK_DIR

RUN pip install wheel sagemaker

COPY main.py .

WORKDIR ${WORK_DIR}
CMD ["main.handler"]
```

and corresponding `main.py`

```python
import sagemaker
import sys

def handler(event, context):
    print(sys.version)
    print(f"sagemaker {sagemaker.__version__}")
    return {
        'success': True
    }
```

As an added bonus, above image can be built and tested locally:

```bash
docker build -t sagemaker-python-sdk-demo:latest .
```

```bash
curl -XPOST "http://localhost:9002/2015-03-31/functions/function/invocations" -d '{}'
{"success": true}
```

```bash
docker run --rm -p 9002:8080 sagemaker-python-sdk-demo:latest

time="2021-06-28T02:12:34.914" level=info msg="exec '/var/runtime/bootstrap' (cwd=/home/app, handler=)"
time="2021-06-28T02:13:21.485" level=info msg="extensionsDisabledByLayer(/opt/disable-extensions-jwigqn8j) -> stat /opt/disable-extensions-jwigqn8j: no such file or directory"
time="2021-06-28T02:13:21.485" level=warning msg="Cannot list external agents" error="open /opt/extensions: no such file or directory"
START RequestId: 0265665a-d04e-4f29-b87d-472f3e7001ae Version: $LATEST
3.8.10 (default, Jun 21 2021, 07:32:26) 
[GCC 7.3.1 20180712 (Red Hat 7.3.1-12)]
sagemaker 2.47.0
END RequestId: 0265665a-d04e-4f29-b87d-472f3e7001ae
REPORT RequestId: 0265665a-d04e-4f29-b87d-472f3e7001ae	Init Duration: 0.45 ms	Duration: 1025.09 ms	Billed Duration: 1100 ms	Memory Size: 3008 MB	Max Memory Used: 3008 MB
```

>> Note the memory consumed above

Review [Amazon ECR Registry upload](https://docs.aws.amazon.com/lambda/latest/dg/images-create.html) and [deployment](https://docs.aws.amazon.com/lambda/latest/dg/configuration-images.html) documentation
