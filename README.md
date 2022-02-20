# custom-cartographer-supply-chain-examples

Run kapp deploy from repo root:
```
kapp deploy --yes -a demo \
-f <(ytt --ignore-unknown-comments -f .) \
-f <(ytt --ignore-unknown-comments -f ./build/kpack -f values.yml) \
-f <(ytt --ignore-unknown-comments -f ./code-analysis/sonar -f values.yml) \
-f <(ytt --ignore-unknown-comments -f ./deploy/k8s -f values.yml) \
-f ./source

```