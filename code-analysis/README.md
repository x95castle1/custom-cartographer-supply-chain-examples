## Run the code-analysis use case

> For non-java workloads looking to use sonar for code quality analysis, consider using the [tekton sonarqube scanner task](https://hub.tekton.dev/tekton/task/sonarqube-scanner). This [repo](https://gitlab.com/drawsmcgraw/cartographer-sonar) is an example custom supply chain to take advantage of this reusable task.

## Prerequisites

1. Install flux [source-controller](https://fluxcd.io/docs/gitops-toolkit/source-watcher/#install-flux) for providing the ability to find new commits to a git
  repository and making it internally available to other resources.

2. Install the [git-cli task](https://github.com/tektoncd/catalog/tree/main/task/git-cli/0.2) from the
  [tekton catalog](https://github.com/tektoncd/catalog). This is used to write to the git repo.

```bash
kapp deploy --yes -a tekton-git-cli -f https://raw.githubusercontent.com/tektoncd/catalog/main/task/git-cli/0.2/git-cli.yaml
```

3. Install [sonarqube](https://docs.sonarqube.org/latest/setup/sonarqube-on-kubernetes/) (skip if you already have a sonarqube instance running in a cluster)

## Run

> assumes current working directory is `repo-root/code-analysis`

Submit a workload to run through a supply chain with the following steps: 

<span>**get source** &rarr; **run test and sonar code quality analysis** &rarr; **build image** &rarr; **deploy** &rarr; **write config**  &rarr; **stamp out a deliverable** </span> and 

Deploy the deliverable produced from the supply chain through cluster delivery with the following steps: 

<span> **get source from the 'write config' (last step) in the supply chain** &rarr; **deploy** </span>
```
kapp deploy --yes -a code-analysis-demo -c \
-f <(ytt --ignore-unknown-comments -f . -f ../values.yml) \
-f <(ytt --ignore-unknown-comments -f ../shared -f ../values.yml)
```
