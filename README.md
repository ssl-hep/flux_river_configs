# Introduction
This repository holds configuration for servicex instances on river. Each directory has configuration
for a different instance.  The directory name corresponds to the namespace that
the instance exists in.

## Current Status of Each Deployment
[Wiki Page](https://github.com/ssl-hep/flux_river_configs/wiki/What-Deployment-is-Doing-What%3F)

## Instances

**Do not modify the servicex-prod and servicex-int instances since they are used
for CI of production and development code.**  
The testing-1/testing-2/testing-3/testing-4
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


## Globus / Slack secrets

In order for all the integrations to work, you'll need to create 2 secrets

For slack:

* Create a `servicex-slack` secret with address pointing to the webhook
for the flux integration app
* Create a new app for the approval integration:
  * Create a webhook that can send messages to the `#servicex-flux-auths` channel
  * Make sure that webhook url and signing secret are in the `servicex-secrets` secret as `slackSigningSecret` and `slackSignupWebhook`
  * Enable interactivity and set the request url to the servicex app url (e.g. `https://[app-url]/slack`)


For Globus:

* Make sure an app exists for OAuth interactions
* Make sure the ServiceX redirect url is listed (add if not).  The url should be something like `https://[app-url]/auth-callback`
* Generate a new client secret and make sure that it gets into the `servicex-secrets` secret as `globusClientSecret`
* Make sure that `servicex-secrets` secret has `globusClientID` set
