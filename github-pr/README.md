## Run the github pull request use case

This is an enhancement on top of the out of the box supply chain templates provided with TAP such that the config produced from a supply chain gets put in a pull request. Adding a pull request in the workflow provides an opportunity for a review before 'change' is realized in a target environment. This is especially important in highly regulated organizations where change management is enforced where every change affecting production has to be reviewed.

## Assumption
TAP is installed including any of the out of the box supply chains.

## Prerequisites
1. Follow the instructions for making [Live modification of supply chains and templates](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-scc-authoring-supply-chains.html#live-modification-of-supply-chains-and-templates-6). 

    Below are the actual steps taken:
- pause reconciliation of tap meta package
    ```
    kubectl patch packageinstall/tap -n tap-install -p '{"spec":{"paused":true}}' --type=merge
    ```
- pause reconciliation of the supply chain applied in your cluster (this example uses testing-scanning)
    ```
    kubectl patch packageinstall/ootb-supply-chain-testing-scanning -n tap-install -p '{"spec":{"paused":true}}' --type=merge
    ```

2. Add `tekton-taskrun` (ClusterRunTemplate used by config-writer) to the template exclusion list in tap values. 
    ```
    ootb_templates:
    excluded_templates:
        - 'tekton-taskrun'
    ```

    Update tap - `tanzu package installed update tap -p tap.tanzu.vmware.com -v $TAP_VERSION --values-file tap-values.yml -n tap-install`


> assumes current working directory is `repo-root/github-pr`

3. Apply the templates (`kubectl apply -f .`) under supply-chain/config-writer and supply-chain/pull-request. If you prefer to use kapp run `kapp deploy -a custom-templates -f .`

4. Update the ClusterSupplyChain. This example uses the testing scanning implementation. 
    ```
    kubectl replace -f source-test-scan-to-url.yml --replace
    ```

    If you're using either basic or testing supply chain, you can still pick up the enhancements from the full sample (source-test-scan-to-url.yml) provided in this repo and apply the following:
    -  replace the config-writer with the content between comments `# updated config writer template` and `# end updated config writer template`. 
        
        > Note: the sources section will need to be adjusted, for basic - source-provider, for testing - source-tester.

    - add github-pull-request template section

    Cluster supply chain snippet :
    ```
    - name: config-writer
        templateRef:
        kind: ClusterSourceTemplate
        name: config-writer-source-template
        configs:
        - resource: app-config
        name: config
        sources:
        - resource: [source-provider | source-tester]
            name: source
        params:
        - name: serviceAccount
        value: default
        - name: registry
        value:
            repository: [ootb supply chain registry repository provided in tap values]
            server: [ootb supply chain registry server provided in tap values]

    - name: github-pull-request
        templateRef:
        kind: ClusterTemplate
        name: pull-request
        sources:
        - resource: config-writer
            name: config-writer-source-template
    ```