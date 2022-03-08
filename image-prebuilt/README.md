## Run the image-prebuilt use case

## Prerequisites

1. source.apps.tanzu.vmware.com imagerepositories custom resource

## Run
> assumes current working directory is `repo-root/image-prebuilt`:
```
kapp deploy --yes -a image-prebuilt-demo -c \
-f <(ytt --ignore-unknown-comments -f . -f ../values.yml) \
-f <(ytt --ignore-unknown-comments -f ../shared -f ../values.yml)
```