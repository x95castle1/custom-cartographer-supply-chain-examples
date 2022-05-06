## Run the multi-cluster use case

## Assumption
Purposeful TAP clusters (multi-cluster) are setup. At a minimum 2 distinct TAP clusters are involved - build (to turn source code into cloud native image) and run (to deploy).


## Rationale
This is an enhancement on top of the out of the box supply chain provided with TAP to realize a cartographer deliverable produced from a TAP build cluster onto a TAP run cluster. It leverages the custom resource App (kappctrl.k14s.io/v1alpha1) to target the appropriate run cluster where deliverables read from git repositories will be applied.

## Installation:
**The installation steps are to be run in your TAP cluster where you submit a workload (e.g. build cluster).**

1. Follow the instructions for making [Live modification of supply chains and templates](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-scc-authoring-supply-chains.html#live-modification-of-supply-chains-and-templates-6). 

    Below are the actual steps taken:
- pause reconciliation of tap meta package
    ```
    kubectl patch packageinstall/tap -n tap-install -p '{"spec":{"paused":true}}' --type=merge
    ```
- pause reconciliation of the supply chain applied in your cluster (this example uses basic)
    ```
    kubectl patch packageinstall/ootb-supply-chain-basic -n tap-install -p '{"spec":{"paused":true}}' --type=merge
    ```

> assumes current working directory is `repo-root/multi-cluster`

3. Apply the templates (`kubectl apply -f .`) under supply-chain/deliverable-config and supply-chain/deliverable-config -writer. If you prefer to use kapp run `kapp deploy -a custom-templates -f .`

4. Update the ClusterSupplyChain. This example uses the basic supply cgain. 
    ```
    kubectl replace -f source-to-url.yml --replace
    ```

## Notes:

- The secret specified in App.spec.cluster.kubeconfigSecretRef holds kubeconfig info of a target run cluster. This enables 
cross-cluster communication. 
    
    Sample kube-config.yml template using a service account from your target run cluster-namespace.
    ```
    apiVersion: v1
    kind: Config
    clusters:
    - cluster:
        certificate-authority-data: ${ca}
        server: ${server}
    name: ${clusterName}
    contexts:
    - context:
        cluster: ${clusterName}
        user: ${serviceAccount}
    name: ${serviceAccount}@${clusterName}
    current-context: ${serviceAccount}@${clusterName}
    users:
    - name: ${serviceAccount}
    user:
        token: ${token}
    ```

    Sample command to create kubeconfig as a secret. **Note that this secret should be created in your TAP cluster where you submit a workload (e.g. build cluster)**. Expect future enhancements on this. 
    ```
    kubectl -n default create secret generic kubeconfig-deliverable-reconciler-secret --from-file=value=./kubeconfig.yml
    ```
    
