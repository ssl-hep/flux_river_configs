# Introduction
This repository holds configuration for servicex instances on river.  Each directory has configuration
for a different instance.  The directory name corresponds to the namespace that
the instance exists in.

## Instances

**Do not modify the prod-xaod/prod-uproot or int-xaod/int-prod** instances since they are used
for CI of production and development code.  The testing-1/testing-2/testing-3
instances can be used for testing purposes, just let the slack channel know in
order to avoid using an instance that is being used by someone else.


## Creating secrets

Each instance needs to have a sealed secret in the namespace in order to work.
To create a new sealed secret, create a file with the following

```
apiVersion: v1
data:
  minio-accesskey: 
  minio-secretkey: 
  rabbitmq-password: 
  rabbitmq-erlang-cookie: 
  postgresql-postgres-password: 
kind: Secret
metadata:
  annotations:
    sealedsecrets.bitnami.com/managed: "true"
    sealedsecrets.bitnami.com/namespace-wide: "true"  
  name: servicex-secrets
  namespace: 
type: Opaque
```

and add in the appropriate values (as base64 encoded values) for the fields in the `data` section  as well as
the namespace in the `metadata` section.  Then run 
`kubeseal  --format yaml -n kube-system --controller-name=sealed-secrets < [secrets-file]  > [sealed-secrets-file]`.
The sealed-secrets-file should be checked in to the repo with the appropriate
name.  *Do not check the secrets-file that you generated in to the repository*
Keep the secrets file in a safe place since it has the various passwords for the
ServiceX components.

