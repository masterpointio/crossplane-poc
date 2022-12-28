# crossplane-poc
POC to build, run, and operate our own internal AWS cloud platform via Crossplane.

This repo is forked from https://github.com/upbound/platform-ref-aws#build-and-push-your-platform, and then modified for our needs.

## Steps

### Install Upbound Universal Crossplane (UXP)
This installs an Upbound's official enterprise-grade distribution of Crossplane to `upbound-system` Kuberneted Namespace.
```bash
brew install upbound/tap/up
up uxp install
```

### Install and Configure Providers

#### Installation

```bash
kubectl apply -f providers/ -R
```

Validate that all of them are healthy:
```bash
kubectl get provider
NAME                  INSTALLED   HEALTHY   PACKAGE                                                         AGE
provider-aws          True        True      xpkg.upbound.io/upbound/provider-aws:v0.22.0                    3d6h
provider-helm         True        True      xpkg.upbound.io/crossplane-contrib/provider-helm:v0.12.0        90m
provider-kubernetes   True        True      xpkg.upbound.io/crossplane-contrib/provider-kubernetes:v0.5.0   79m
```

#### Configuration for `provider-aws`

There is a service user `crossplane-poc-service` created manually. Its Access Key+Secret are used to configure the provider.

```bash

# Create a K8s secret with the AWS creds:
kubectl create secret generic aws-creds -n upbound-system --from-file=credentials=./creds.conf
echo -e "[default]\naws_access_key_id = $ACCESS_KEY_ID\naws_secret_access_key = $ACCESS_SECRET_KEY" > credentials.conf
kubectl create secret generic aws-creds -n upbound-system --from-file=credentials=./credentials.conf
kubectl apply -f examples/aws-default-provider.yaml
```

### Apply Cluster

```bash
kubectl apply -f package/cluster/network/definition.yaml
kubectl apply -f package/cluster/network/composition.yaml

kubectl apply -f package/cluster/eks/definition.yaml
kubectl apply -f package/cluster/eks/composition.yaml

kubectl apply -f package/cluster/definition.yaml
kubectl apply -f package/cluster/composition.yaml

kubectl apply -f examples/cluster-claim.yaml
```
