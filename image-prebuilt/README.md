## Run the image-prebuilt use case

## Prerequisites

1. source.apps.tanzu.vmware.com imagerepositories custom resource
    https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-source-controller-about.html

2. scanning.apps.tanzu.vmware.com/v1beta1 ImageScan

    https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-scst-scan-overview.html


## Run
> assumes current working directory is `repo-root/image-prebuilt`:
```
kapp deploy --yes -a image-prebuilt-demo -c \
-f <(ytt --ignore-unknown-comments -f . -f ../values.yml) \
-f <(ytt --ignore-unknown-comments -f ../shared -f ../values.yml)
```