---
title: "Venafi"
linkTitle: "Venafi"
weight: 40
type: "docs"
---

The Venafi `Issuer` types allows you to obtain certificates from [Venafi
Cloud](https://pki.venafi.com/venafi-cloud) and [Venafi Trust Protection
Platform](https://venafi.com) instances.

Register your account at https://ui.venafi.cloud/enroll and get an API key from
your dashboard.

You can have multiple different Venafi `Issuer` types installed within the same
cluster, including mixtures of Cloud and TPP issuer types. This allows you to be
flexible with the types of Venafi account you use.

Automated certificate renewal and management are provided for `Certificates`
using the Venafi issuer.

## Creating an Issuer resource

A single Venafi `Issuer` represents a single 'zone' within the Venafi API,
therefore you must create an `Issuer` resource for each Venafi Zone you want to
obtain certificates from.

You can configure your `Issuer` resource to either issue certificates only
within a single namespace, or cluster-wide (using a `ClusterIssuer` resource).
For more information on the distinction between `Issuer` and `ClusterIssuer`
resources, read the [`Issuer` vs
`ClusterIssuer`](../../concepts/issuer/) section.

### Creating a Venafi Cloud Issuer

In order to set up a Venafi Cloud Issuer, you must first create a Kubernetes
Secret resource containing your Venafi Cloud API credentials:

```bash
$ kubectl create secret generic \
       cloud-secret \
       --namespace='NAMESPACE OF YOUR ISSUER RESOURCE' \
       --from-literal=apikey='YOUR_CLOUD_API_KEY_HERE'
```

> **Note**: If you are configuring your issuer as a `ClusterIssuer` resource in
> order to issue Certificates across your whole cluster, you must set the
> `--namespace` parameter to `kube-system`, which is the default `Cluster
> Resource Namespace`. The `Cluster Resource Namespace` can be configured
> through the `--cluster-resource-namespace` flag on the cert-manager controller
> component.

This API key will be used by cert-manager to interact with the Venafi Cloud
service on your behalf.

Once the API key Secret has been created, you can create your `Issuer` or
`ClusterIssuer` resource. If you are creating a `ClusterIssuer` resource, you
must change the `kind` field to `ClusterIssuer` and remove the
`metadata.namespace` field.

Save the below content after making your amendments to a file named
`venafi-cloud-issuer.yaml`:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Issuer
metadata:
  name: cloud-venafi-issuer
  namespace: <NAMESPACE YOU WANT TO ISSUE CERTIFICATES IN>
spec:
  venafi:
    zone: "DevOps" # Set this to the Venafi policy zone you want to use
    cloud:
      apiTokenSecretRef:
        name: cloud-secret
        key: apikey
```

You can then create the Issuer using `kubectl create`:

```bash
$ kubectl create -f venafi-cloud-issuer.yaml
```

Verify the `Issuer` has been initialized correctly using `kubectl describe`:

```bash
$ kubectl describe issuer cloud-venafi-issuer --namespace='NAMESPACE OF YOUR ISSUER RESOURCE'
```

* TODO: add output

You are now ready to issue certificates using the newly provisioned Venafi
Issuer.

Read the [Issuing Certificates](../../usage/certificate/) document for
more information on how to create Certificate resources.


### Creating a Venafi Trust Protection Platform Issuer

The Venafi Trust Protection integration allows you to obtain certificates from
a properly configured Venafi TPP instance.

The setup is similar to the Venafi Cloud configuration above, however some of
the connection parameters are slightly different.

> Note: You *must* allow "User Provided CSRs" as part of your TPP policy, as
> this is the only type supported by cert-manager at this time.

In order to set up a Venafi Trust Protection Platform Issuer, you must first
create a Kubernetes Secret resource containing your Venafi TPP API credentials:

```bash
$ kubectl create secret generic \
       tpp-secret \
       --namespace=<NAMESPACE OF YOUR ISSUER RESOURCE> \
       --from-literal=username='YOUR_TPP_USERNAME_HERE' \
       --from-literal=password='YOUR_TPP_PASSWORD_HERE'
```

> Note: If you are configuring your issuer as a `ClusterIssuer` resource in
> order to issue Certificates across your whole cluster, you must set the
> `--namespace` parameter to `kube-system`, which is the default `Cluster
> Resource Namespace`. The `Cluster Resource Namespace` can be configured
> through the `--cluster-resource-namespace` flag on the cert-manager controller
> component.

These credentials will be used by cert-manager to interact with your Venafi TPP
instance. Username attribute must be adhere to the `<identity
provider>:<username>` format.  For example: `local:admin`.

Once the Secret containing credentials has been created, you can create your
`Issuer` or `ClusterIssuer` resource. If you are creating a `ClusterIssuer`
resource, you must change the `kind` field to `ClusterIssuer` and remove the
`metadata.namespace` field.

Save the below content after making your amendments to a file named
`venafi-tpp-issuer.yaml`:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Issuer
metadata:
  name: tpp-venafi-issuer
  namespace: <NAMESPACE YOU WANT TO ISSUE CERTIFICATES IN>
spec:
  venafi:
    zone: devops\cert-manager # Set this to the Venafi policy zone you want to use
    tpp:
      url: https://tpp.venafi.example/vedsdk # Change this to the URL of your TPP instance
      caBundle: <base64 encoded string of caBundle PEM file, or empty to use system root CAs>
      credentialsRef:
        name: tpp-secret
```

You can then create the `Issuer` using `kubectl create -f`:

```bash
$ kubectl create -f venafi-tpp-issuer.yaml
```

Verify the `Issuer` has been initialized correctly using `kubectl describe`:

```bash
$ kubectl describe issuer tpp-venafi-issuer --namespace='NAMESPACE OF YOUR ISSUER RESOURCE'
```

You are now ready to issue certificates using the newly provisioned Venafi
Issuer.

Read the [Issuing Certificates](../../usage/certificate/) document for
more information on how to create Certificate resources.
