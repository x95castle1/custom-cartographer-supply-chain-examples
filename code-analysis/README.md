## Run the code-analysis use case

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