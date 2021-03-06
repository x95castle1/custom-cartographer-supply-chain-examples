## Run the image-prebuilt use case

This example can be run alongside an existing TAP installation that has the out of the box supply chain - testing and scanning.
If you wish to run this on a cluster that doesnt have TAP, the steps in the prerequisites section are required.
Either approaches (with or without pre-existing TAP install) have been validated and should work.

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

4. Install Supply Chain Security Tools - Store
```bash
tanzu package install metadata-store --package-name metadata-store.apps.tanzu.vmware.com --version [x.x.x]
```


## Run
> assumes current working directory is `repo-root/image-prebuilt`:
```
kapp deploy --yes -a image-prebuilt-demo -c \
-f <(ytt --ignore-unknown-comments -f . -f ../values.yml) \
-f <(ytt --ignore-unknown-comments -f ../shared -f ../values.yml)
```

## View scan results
Follow the instructions [here](https://docs.vmware.com/en/Tanzu-Application-Platform/1.0/tap/GUID-scst-store-additional.html)