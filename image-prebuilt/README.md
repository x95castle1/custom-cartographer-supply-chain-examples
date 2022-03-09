## Run the image-prebuilt use case

## Prerequisites
1. Add Tanzu TAP repository

```bash
export INSTALL_REGISTRY_USERNAME='[tanzu net username]'
export INSTALL_REGISTRY_PASSWORD='[tanzu net password]'
export INSTALL_REGISTRY_HOSTNAME=registry.tanzu.vmware.com
export TAP_VERSION=[version]

tanzu secret registry add tap-registry \
  --username ${INSTALL_REGISTRY_USERNAME} --password ${INSTALL_REGISTRY_PASSWORD} \
  --server ${INSTALL_REGISTRY_HOSTNAME} \
  --export-to-all-namespaces --yes

tanzu package repository add tanzu-tap-repository \
  --url registry.tanzu.vmware.com/tanzu-application-platform/tap-packages:$TAP_VERSION
```

2. Install Tanzu Source Controller
```bash
tanzu package install source-controller -p controller.source.apps.tanzu.vmware.com -v [x.x.x]
```

3. Install Supply Chain Security Tools - Scan
```bash
tanzu package install scan-controller --package-name scanning.apps.tanzu.vmware.com --version [x.x.x]
```

## Run
> assumes current working directory is `repo-root/image-prebuilt`:
```
kapp deploy --yes -a image-prebuilt-demo -c \
-f <(ytt --ignore-unknown-comments -f . -f ../values.yml) \
-f <(ytt --ignore-unknown-comments -f ../shared -f ../values.yml)
```