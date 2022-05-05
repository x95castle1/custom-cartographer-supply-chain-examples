## Run the multi-cluster use case

## Assumption
Purposeful TAP clusters (multi-cluster) are setup. At a minimum 2 distinct TAP clusters are involved - build (to turn source code into cloud native image) and run (to deploy).


## Rationale
This is an enhancement on top of the out of the box supply chain provided with TAP to realize a cartographer deliverable produced from a TAP build cluster onto a TAP run cluster. It leverages the custom resource App (kappctrl.k14s.io/v1alpha1) to target the appropriate run cluster where deliverables read from git repositories will be applied.