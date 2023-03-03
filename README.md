# nats-helm-mybasic

This is an example of using the NATS helm chart as a sub-package allowing arbitrarily complex k8s deployment manifest.

This environment is the basic of basics: basic auth for NATS users and no-TLS fun.

## Big idea

Leaverage the NATS helm chart in the context of a larger (and more complicated) k8s deployment,
e.g. configuring load balancer services and other environment specific setups that are not directly
part of the NATS helm chart.

### Chart.yaml dependency

Note that the NATS helm chart added as a dependency in `Chart.yaml`. This is a mechanism (and a responsibility) to
set a specific version of the NATS helm chart in your project.

- Set version of NATS helm chart in `Chart.yaml` then run `helm dep update`.

### values.yaml

Note the namespace nesting in `values.yaml` (an additional `nats:` indention) that reflects subpackage.

## This chart

For demonstration:

- Adds a load balancer service, `templates/services-lb.yaml`, that exposes the NATS listener (and monitor)
to k8s-external clients
- Specifies a specific NATS release in `values.yaml`

### Deployment

`helm install -f values.yaml <deploy name> .`

To update:

`helm upgrade -f values.yaml <deploy name> .`

### Verification

```bash
$ kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
mynatsjsacc-box-67dd69cbdd-vgtpr   1/1     Running   0          16m
mynatsjsacc-3                      3/3     Running   0          16m
mynatsjsacc-2                      3/3     Running   0          15m
mynatsjsacc-1                      3/3     Running   0          15m
mynatsjsacc-0                      3/3     Running   0          15m
$ kubectl exec --stdin --tty mynatsjsacc-box-67dd69cbdd-vgtpr -- /bin/sh
~ # uptime
 20:47:08 up 29 min,  0 users,  load average: 0.11, 0.09, 0.04
```

```bash
# In a nats-box shell, you can message as SYS account (user System):
nats --server "nats://System:s3cr3t@$NATS_URL:4222" server ls

# Or as the non-SYS account AcctA that is JetStream enabled (user UserA1):
nats --server "nats://UserA1:s3cr3t@$NATS_URL:4222" str ls
```

## See also

- [NATS Helm Chart repo: "Using NATS chart as dependency"](https://github.com/nats-io/k8s/tree/main/helm/charts/nats)