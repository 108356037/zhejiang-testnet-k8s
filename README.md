# Zhejiang Testnet @ K8s

## Introduction

This repo is a guide for setting up consensus client and execution client for Zhejiang testnet on Kubernetes.

## Prerequisites

Any Kubernetes platform should be fine, nevertheless performance of storage speed is still crucial. For my case, I run on GKE with node pool using `e2-highcpu-8`, along with `premium-rwo` storage class.

## Installation

1. Clone the repo and `cd` into it:

```
git clone https://github.com/108356037/zhejiang-testnet-k8s.git
cd zhejiang-testnet-k8s
```

2. Create a dedicated namespace:

```
kubectl create -f ./k8s-yamls/namespace.yaml
```

3. Create the JWT password for communication between consensus client and execution client:

```
JWT=$(openssl rand -hex 32)
kubectl -n zhejiang-testnet create secret generic el-cl-jwt-secret --from-literal password=$JWT
```

4. Deploy consensus client and execution client:

```bash
# For exectution client (using Besu)
kubectl create -f ./k8s-yamls/besu-depl.yaml

# For consensus client (using Lighthouse)
kubectl create -f ./k8s-yamls/lighthouse-depl.yaml
```

It should not take too long to sync zhejiang testnet nodes, in my case it took about 20~30 minutes for both clients to fully synchronized. From my experiments, if Besu is unresponsive even after Lighthouse is fully synchronized, try manually triggering a restart on the Besu pod.

## Why Besu+Lighthouse?

I initially tried out Geth+Lighthouse, but as many reported, there currently seems to be a bug for this combo on Zhejiang testnet: https://github.com/ethereum/go-ethereum/issues/26647. Therefore I switched to Besu+Lighthouse and discovered this combo works pretty well on Kubernetes.

## Interact with the clients

An exploration with the new clients and withdrawals can be found here: https://mirror.xyz/daishioubu.eth/hyIn44oruz7FtezwhZV6EA5jiPSaq5lvOZQ4OY8VfGY

## Reference

- [How to run a node on the Zhejiang testnet?](https://notes.ethereum.org/@launchpad/zhejiang#How-to-run-a-node-on-the-Zhejiang-testnet)
- [Testing Beacon Chain Withdrawals with Docker](https://mirror.xyz/ladislaus.eth/nYZCmrO7ZndrP92kGyJG0-wzbnIsd3HwxsII-gT1zu8)

###### tags: `ethereum` `zhejiang-testnet` `EIP-4895`
