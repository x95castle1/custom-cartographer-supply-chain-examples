run from `repo-root/image-prebuilt`:
```
kapp deploy --yes -a image-prebuilt -c \
-f <(ytt --ignore-unknown-comments -f . -f ../values.yml) \
-f <(ytt --ignore-unknown-comments -f ../shared -f ../values.yml)
```