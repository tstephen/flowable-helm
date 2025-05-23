# Flowable

This is a helm chart for the Flowable UI and Flowable REST.

## TL;DR;

```console
helm repo add flowable-oss https://flowable.github.io/helm/

helm install my-flowable flowable-oss/flowable
```

## Installing the Chart

To install the *local* chart with the release name `my-flowable`:

```console
helm install my-flowable .
```

To install the *repo* chart with the release name `my-flowable`:

```console
helm repo add flowable-oss https://flowable.github.io/helm/

helm install my-flowable flowable-oss/flowable \
    --create-namespace --namespace=flowable \
    --set host.external=<cluster external hostname> --set ingress.useHost=true \
    --set postgres.storage.storageClassName=default
```

This will install Flowable as the *my-flowable* release in the *flowable* namespace.

It will also configure Ingress mapping rules for route on the specified *host*.
This requires an Ingress controller to be active on the cluster. For more info see the main [README](https://github.com/flowable/flowable-engine/blob/main/k8s/README.md).

The *StorageClassName* will be set to *default*.

## Uninstalling the Chart

To uninstall/delete the `my-flowable` deployment:

```console
helm remove my-flowable
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Signed Chart

Starting with version 6.7.3 the Flowable Helm chart is signed.

The public PGP key can be obtained via keyservers: [keybase.io](https://keybase.io/flowable_oss/pgp_keys.asc) and [pgp.mit.edu](https://pgp.mit.edu/pks/lookup?op=get&search=0xA79624786AE252EB).
Or in this [repository](https://github.com/flowable/helm/blob/main/flowable_oss.asc).

There are several tools that provide support for verifying the integrity and origin of the chart. Verifying manually can be done following the steps below.

### Obtain Public Key

Import key from keybase.io

```
curl https://keybase.io/flowable_oss/pgp_keys.asc | gpg --import
```

or

Import key from pgp.mit.edu

```
gpg --keyserver pgp.mit.edu --recv-keys 6AE252EB
```

### Export Public Keyring in GPG format

After importing the public key into your local keyring you need to export it to the `.gpg` format using the following command.

```
gpg --output pubring.gpg --export 6AE252EB
```

### Verify Signed Helm Chart

Add the Flowable Helm chart this was not done yet.

```
helm repo add flowable-oss https://flowable.github.io/helm/
```

You can then use `helm fetch --verify` to verify the signed chart.

```
helm fetch --verify flowable_oss/flowable --version 7.0.0 --keyring pubring.gpg
```

## Chart Configuration

The following tables lists the configurable parameters of the Flowable chart and their default values.

| Parameter                                     | Description                                                                                                           | Default                       |
| --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- | ----------------------------- |
| `host.external`                               | External cluster IP or FQDN                                                                                           | `localhost`                   |
| `database.name`                               | Database name                                                                                                         | `flowable`                    |
| `database.username`                           | Database user                                                                                                         | `flowable`                    |
| `database.password`                           | Database password                                                                                                     | `flowable`                    |
| `cloudSql.enabled`                            | Enables Google Cloud SQL for database                                                                                 | `false`                       |
| `cloudSql.credentials`                        | Google Cloud SQL credentials secret reference                                                                         | `cloudsql-credentials.json`   |
| `ingress.enabled`                             | Enables Ingres                                                                                                        | `true`                        |
| `ingress.sslRedirect`                         | Enables SSL redirect                                                                                                  | `false`                       |
| `ingress.useHost`                             | Enables host based routing using external `host.external` ( this must be a FQDN)                                           | `false`                            |
| `ingress.class`                               | Ingress class name                  | `nginx`
| `ingress.clusterIssuer`                               | Ingress cert manager cluster issuer                  | ``
|<br/>|
| `rest.enabled`                                | Enables Flowable REST (either enable Flowable UI or Flowable REST)                                                  | `true`                       |
| `rest.replicas`                               | Number of replicated pods                                                                                             | `1`                           |
| `rest.service.name`                           | Kubernetes service name                                                                                               | `flowable-rest`               |
| `rest.contextPath`                             | Tomcat servlet mapping                                                                                                | `/`                           |
| `rest.ingressPath`                            | Ingress path mapping                                                                                                  | `flowable-rest`               |
| `rest.image.repository`                       | Docker image name                                                                                                     | `flowable/flowable-rest`      |
| `rest.image.tag`                              | Docker tag name                                                                                                       | `latest`                      |
| `rest.image.pullPolicy`                       | Docker pull policy                                                                                                    | `Always`                      |
| `rest.resources.requests.cpu`                 | Kubernetes CPU request                                                                                                | `100m`                        |
| `rest.resources.requests.memory`              | Kubernetes memory request                                                                                             | `1Gi`                         |
| `rest.resources.limits.cpu`                   | Kubernetes CPU limit                                                                                                  | `1`                           |
| `rest.resources.limits.memory`                | Kubernetes memory limit                                                                                               | `1Gi`                         |
| `rest.resources.javaOpts`                     | JVM options                                                                                                           | `-Xmx1g -Xms1g`               |
| `rest.admin.username`                         | Admin user to create                                                                                                  | `rest-admin`                  |
| `rest.admin.password`                         | Password for admin user                                                                                               | `test`                  |
| `rest.readinessProbe.initialDelaySeconds`     | Time to allow pod to start before first probe                                                                         | `60`                          |
| `rest.readinessProbe.periodSeconds`           | Time between probes                                                                                                   | `10`                          |
| `rest.readinessProbe.timeoutSeconds`          | Time allowed for pod to respond to probe                                                                              | `5`                           |
| `rest.readinessProbe.successThreshold`        | Number of successful probes before marking pod ready                                                                                                                                                                   | `1`                           |
| `rest.readinessProbe.failureThreshold`        | Number of failed probes before recycling the pod                                                                                                                                                                     | `3`                           |
| `rest.livenessProbe.initialDelaySeconds`      | Time to allow pod to start before first probe                                                                         | `60`                          |
| `rest.livenessProbe.periodSeconds`            | Time between probes                                                                                                   | `10`                          |
| `rest.livenessProbe.timeoutSeconds`           | Time allowed for pod to respond to probe                                                                              | `10`                          |
| `rest.livenessProbe.successThreshold`         | Number of successful probes before marking pod live                                                                                                                                                                    | `1`                           |
| `rest.livenessProbe.failureThreshold`         | Number of failed probes before recycling the pod                                                                                                                                                                     | `10`                          |
|<br/>|
| `postgres.enabled`                            | Will deploy and configure a PostgreSQL database instance                                                              | `true`                        |
| `postgres.service.name`                       | Kubernetes service name                                                                                               | `flowable-postgres`           |
| `postgres.storage.storageClassName`           | Kubernetes storage class name for peristent volume claim                                                              | `standard`                    |
| `postgres.storage.size`                       | Peristent volume claim resource request size                                                                          | `1Gi`                         |
| `postgres.resources.requests.cpu`             | Kubernetes CPU request                                                                                                | `100m`                        |
| `postgres.resources.requests.memory`          | Kubernetes memory request                                                                                             | `1Gi`                         |
| `postgres.resources.limits.cpu`               | Kubernetes CPU limit                                                                                                  | `1000m`                       |
| `postgres.resources.limits.memory`            | Kubernetes memory limit                                                                                               | `1Gi`                         |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example;

```console
helm install my-flowable flowable-oss/flowable \
  --set postgres.enabled=false
```

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example;

```console
helm install my-flowable flowable-oss/flowable -f values.yaml
```

[flowable]: https://github.com/flowable/flowable-engine
