# Helm Chart for Google Hello App

This Helm chart deploys the Google Hello App with MySQL as backing database.

The source code of the app can be found at: https://github.com/GoogleCloudPlatform/kubernetes-engine-samples/tree/master/hello-app

## Notes

- first create initial chart: `helm create hello-cloudeteer`
- after adding the Bitnami MySQL dependency to Chart.yaml, run `helm dependency build`
- I removed the HPA and Ingress from the created default chart, as those are not needed for.

## Installation

After cloning the repo, install with `helm install hello hello-cloudeteer`, or customize the values file and run `helm install hello hello-cloudeteer -f values.yaml`

## Persistent Storage

One objective was to have the application backed by a Kubernetes a deployment, while at the same time having the option to enabled persistent storage for it. Also, the count of replicas is configurable, which means in combination with the persistent storage option for the app, the documentation needs to state the restrictions on the volume access mode and replica count combination that can be used in such a scenario.

When using persistent storage and multiple replicas at the same time, all Pods in a Deployment will mount the same PVC. However, with the default block storage access mode of `ReadWriteOnce` this is only possible, if all Pods run on the same Kubernetes node. However, this usually defeats the purpose of having multiple replicas for availability reasons in the first place.

In this case, you will need to mount a PVC from a Storage Class that supports the `ReadWriteMany` access mode (such as NFS) or mount the volume as `ReadOnlyMany`.

A different option would be build the service a StatefulSet, but this was not the requirement here.

**NOTE:** This is independent of the MySQL service that is deployed as a dependency of this Helm chart. The MySQL service will per default be deployed as a single-instance server with its own persistent storage enabled.

## Accessing the service

Depending on the service type, you can either access the service only from within the cluster or via `kubectl port-forward svc/hello-hello-cloudeteer 8080` (`ClusterIP`, which is the default) or from the outside (via a `NodePort` or `LoadBalancer` service type). The service will not be deleted upon uninstall of the Helm release. This makes sure that the app stays registered in the cluster's internal DNS (even when no backing Pod endpoints exist anymore).

There is no Ingress configured with this Helm Chart for now. This can be added in a future release, if user requirements make this needed.
