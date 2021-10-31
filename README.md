# Source for https://gliptak.github.io/

Commits on ```hugo``` branch deployed using ```Github Actions```

https://gohugo.io/hugo-modules/use-modules/

hugo version v0.84.1

```
git submodule add https://github.com/gcushen/hugo-academic.git themes/hugo-academic
cd themes/hugo-academic
git checkout v5.2.0
```
## New content

docker run --rm -v $(pwd):/data -w /data hugo:latest new post/my_first_post/index.md

docker run --rm -v $(pwd):/data -w /data -p 1313:1313 hugo:latest server -D --bind 0.0.0.0
