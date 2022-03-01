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

- [kpack](https://github.com/pivotal/kpack/blob/main/docs/install.md),
  for providing an opinionated way of continuously building container
  images using buildpacks

- [source-controller](https://fluxcd.io/docs/gitops-toolkit/source-watcher/#install-flux),
  for providing the ability to find new commits to a git
  repository and making it internally available to other resources

- [kapp-controller](https://carvel.dev/kapp-controller/docs/latest/install/),
  for providing us with the ability of deploying multiple
  Kubernetes objects as a single unit

4. A container image registry in which you have authorization to push images. The
  credentials will be passed to kpack in the configuration steps below.

5. [Tree](https://github.com/ahmetb/kubectl-tree), a tool that we will use to observe the objects created.

6. Install [tekton]

```bash
TEKTON_VERSION=0.30.0 kubectl apply -f https://storage.googleapis.com/tekton-releases/pipeline/previous/v$TEKTON_VERSION/release.yaml
```

7. Install the [git-cli task](https://github.com/tektoncd/catalog/tree/main/task/git-cli/0.2) from the
  [tekton catalog](https://github.com/tektoncd/catalog). This is used to write to the git repo.

```bash
kapp deploy --yes -a tekton-git-cli -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/git-cli/0.2/git-cli.yaml
```

## Run the example in this repo 

> assumes current working directory is `repo root`

Deploy workload to run through supply chain with the following steps: <span style="color: green;"> **get source** &rarr; **run test and sonar code quality analysis** &rarr; **build image** &rarr; **deploy** &rarr; **write config** </span> AND
Deploy deliverable through cluster delivery with the following steps: <span style="color: green;"> **get source from the 'write config' (last step) in the supply chain** &rarr; **deploy** </span>
```
kapp deploy --yes -a demo -c \
-f <(ytt --ignore-unknown-comments -f workload.yml -f values.yml) \
-f <(ytt --ignore-unknown-comments -f ./shared -f values.yml) \
-f <(ytt --ignore-unknown-comments -f ./code-analysis -f values.yml)
```

