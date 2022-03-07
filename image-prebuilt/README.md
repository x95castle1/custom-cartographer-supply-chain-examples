## Run the image-prebuilt use case

> assumes current working directory is `repo-root/image-prebuilt`:
```
kapp deploy --yes -a image-prebuilt-demo -c \
-f <(ytt --ignore-unknown-comments -f . -f ../values.yml) \
-f <(ytt --ignore-unknown-comments -f ../shared -f ../values.yml)
```