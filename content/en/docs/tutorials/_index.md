---
title: "Tutorials"
linkTitle: "Tutorials"
weight: 50
type: "docs"
---

It is strongly recommended that users are familiar with concepts and
configuration of resources in the documentation when using cert-manager
however, set-by-step tutorials are very useful for deploying these resources to
get to a final goal. Below is list of tutorials that you might find helpful:

- [Backup and Restore Resources](./backup/): Backup the cert-manager resources
  in your cluster and then restore them.
- [Uninstalling cert-manager](./uninstall/): Completely uninstall
  cert-manager from your cluster.
- [Securing Ingresses with NGINX-Ingress and
  cert-manager](./acme/ingress/): Tutorial for deploying NGINX into your
  cluster and securing the connection with an ACME certificate.
- [Issuing ACME Certificate using DNS Validation](./acme/dns-validation/):
  Tutorial on how to resolve DNS ownership validation using DNS01 challenges.
- [Issuing ac ACME Certificate using HTTP Validation](./acme/http-validation/):
  Tutorial on how to resolve DNS ownership validation using HTTP01 challenges.
- [Migrating from kube-lego](./acme/migrating-from-kube-lego/): Tutorial on
  how to migrate from the now deprecated kube-lego project.
- [Securing an EKS Cluster with Venafi](./venafi/venafi/): Tutorial for
  creating an EKS cluster and securing an NGINX deployment with a Venafi issued
  certificate.
