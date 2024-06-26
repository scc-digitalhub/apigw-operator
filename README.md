# API Gateway Operator

A Kubernetes operator to launch ingresses for services.

## Installation
A number of environment variables must be configured. If you're using the `deployment.yaml` file, you will find them towards the end of the file.
```
WATCH_NAMESPACE: apigw-operator-system
ENABLE_TLS: true
TLS_SECRET_NAME: ingresssecret
INGRESS_CLASS_NAME: nginx
```

Install operator and CRD:
```sh
kubectl apply -f deployment.yaml
```

An example CR is found at `config/samples/operator_v1_apigw.yaml`. Apply it with:
```sh
kubectl apply -f config/samples/operator_v1_apigw.yaml
```

## Custom Resources
The custom resource's properties are:

- `host`: **Required**. URL to host the access point on.
- `path`: **Required**. Path within the host to host the access point on.
- `service`: **Required**. Name of the Kubernetes service.
- `port`: **Required**. Internal port the service is listening on.
- `auth`: *Optional*. A structure to configure authentication. If left empty, authentication is disabled. Must be disabled if `INGRESS_CLASS_NAME` is not `nginx`, or resources will result in error. Has the following properties:
  - `type`: `basic` or `none` (disabled). 
  - `basic`: Structure for basic authentication. Has the following properties:
    - `user`
    - `password`

A valid sample spec configuration is:
``` yaml
...
spec:
  host: foo.bar.com
  path: /
  service: myservice
  port: 9080
```

Another valid sample (requires `nginx` as `INGRESS_CLASS_NAME`):
``` yaml
...
spec:
  host: foo.bar.com
  path: /
  service: myservice
  port: 9080
  auth:
    type: basic
    basic:
      user: user
      password: password
```

## Commands for development
This section is for convenience purposes and briefly recaps some commands used in operator development.

**Generate yaml resource files** (run this after editing `*_types.go` files):
```sh
make generate
```

**Generate CRD manifests** (run this after editing `//+kubebuilder` annotations):
```sh
make manifests
```

**Run operator locally** outside the cluster, for testing purposes (`export` required envs first):
```sh
make install run
```

**Generate *deployment.yaml* file** (remember to configure `env` and `image` in the *Deployment* resource):
```sh
./bin/kustomize build config/default > deployment.yaml
```
