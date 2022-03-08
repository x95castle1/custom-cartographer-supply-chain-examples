# custom-cartographer-supply-chain-examples

## Prerequisites

1. Kubernetes v1.19+

```bash
kind create cluster --image kindest/node:v1.21.1
```

2. [Carvel] tools for templating and groups of Kubernetes objects to the api
   server

  - [ytt]: templating the credentials
  - [kapp]: submitting objects to Kubernetes

3. Cartographer, and dependencies used in the example

To install `cartographer`, refer to [README.md](../../README.md).

All that `cartographer` does is choreograph the passing of results from a
Kubernetes object to another, following the graph described in the
[ClusterSupplyChain] object.

This means that `cartographer` by itself is not very useful - its powers arise
from integrating other Kubernetes resources that when tied together with a
supplychain, forms something powerful.

Install the following utilities as well as Cartographer!

- [kpack](https://github.com/pivotal/kpack/blob/main/docs/install.md),
  for providing an opinionated way of continuously building container
  images using buildpacks


- [kapp-controller](https://carvel.dev/kapp-controller/docs/latest/install/),
  for providing us with the ability of deploying multiple
  Kubernetes objects as a single unit

4. A container image registry in which you have authorization to push images. The
  credentials will be passed to kpack in the configuration steps below.

5. [Tree](https://github.com/ahmetb/kubectl-tree), a tool that we will use to observe the objects created.

6. Install [tekton]

```bash
export TEKTON_VERSION=0.30.0 
kubectl apply -f https://storage.googleapis.com/tekton-releases/pipeline/previous/v$TEKTON_VERSION/release.yaml
```

7. Additional setup and instructions to run the examples here are in the readme doc per usecase subfolder.

