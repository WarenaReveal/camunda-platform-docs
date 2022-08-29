---
id: amazon-eks
title: "Amazon EKS"
description: "Deploy Camunda Platform 8 Self-Managed on Amazon EKS."
---

Amazon Elastic Kubernetes Service ([Amazon EKS](https://aws.amazon.com/eks/)) is a managed
container service to run and scale Kubernetes applications in the cloud or on-premises.

Camunda Platform 8 Self-Managed can be deployed on EKS like any Kubernetes cluster. However, there are a few pitfalls to avoid as described below.

## EKS cluster specification

Generally speaking, the EKS cluster specification depends on your needs and workloads.
Here is a recommended start to run Camunda Platform 8:

- Instance type: `m5.xlarge`
- Number of nodes: `4`
- Volume type: `SSD gp3`

:::caution
To use `SSD gp3` volume type on 1.22 or an earlier cluster, you need to install
[Amazon EBS CSI driver](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html).
If you are already on 1.23 or later, the driver is already installed.

The next step is to create a new
[StorageClass](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)
that uses the Amazon EBS `gp3` volume type. Then, use it cluster-wide as a default
`StorageClass` or set it in your values file under `zeebe.pvcStorageClassName`.
:::

## Pitfalls to avoid

:::note
For general deployment pitfalls, visit the
[deployment troubleshooting guide](./troubleshooting.md).
:::

### Volume performance

To have proper performance in Camunda Platform 8, the EKS cluster nodes should use volumes
with around 1,000-3,000 IOPS. The `gp3` volumes deliver a consistent baseline IOPS performance
of 3,000 IOPS. The `gp2` volumes could also be used, but `gp2` volume type performance
[varies based on volume size](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/general-purpose.html#gp2-performance).

It's recommended to use `gp3` volumes, but if only `gp2` type is available, EKS cluster nodes
should use `gp2` volumes of at least 100 GB.