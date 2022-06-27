## Run the knative blue-green deployment use case

Here's an example of a series of changes made to the stamped out knative service - to safely switch traffic to a new deployment (knative revisions) using a cartographer supply chain, following the blue-green deployment strategy:

initial deployment:
```
apiVersion: serving.knative.dev/v1
kind: Service
spec:
  template:
    metadata:
      name: tanzu-java-web-app-d612550247b0e0c50308d363384d5de340ca566424f
      
   traffic:
  - latestRevision: true
    percent: 0
    tag: latest
  - revisionName: tanzu-java-web-app-d612550247b0e0c50308d363384d5de340ca566424f
    percent: 100
```


2nd (subsequent) deployment:
```
apiVersion: serving.knative.dev/v1
kind: Service
spec:
  template:
    metadata:
      name: tanzu-java-web-app-ac9337c4af7b98893e4b00d35a5c4d88a176a4f253a
          
  traffic:
  - latestRevision: true
    percent: 0
    tag: latest
  - revisionName: tanzu-java-web-app-d612550247b0e0c50308d363384d5de340ca566424f
    percent: 100
```


activation (once pull request has been approved and merged):
```
apiVersion: serving.knative.dev/v1
kind: Service
spec:
  template:
    metadata:
      name: tanzu-java-web-app-ac9337c4af7b98893e4b00d35a5c4d88a176a4f253a

  traffic:
  - latestRevision: true
    percent: 0
    tag: latest
  - revisionName: tanzu-java-web-app-ac9337c4af7b98893e4b00d35a5c4d88a176a4f253a
    percent: 100
```