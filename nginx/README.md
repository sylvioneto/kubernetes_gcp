# Nginx on GKE

## Description

This example demonstrates how to deploy the ingress Ngninx on GKE.

## Pre-req:
You must have a GKE cluster ready to received deployments. In case you don't have it, you can create one using [this example](https://github.com/sylvioneto/terraform_gcp/tree/master/gke).


## Deploy
1. Ensure your gcloud is set to the right project.
````
gcloud config list
````

2. Deploy NGINX with Cloud Build informing the cluster's name and region, for example:
```
gcloud builds submit . \
  --config cloudbuild.yaml \
  --substitutions="_GKE_REGION=us-central1","_GKE_CLUSTER=gke-dev"
```

## Known-issues
Access Denied: If you get access denied during the deployment, please review Cloud Build's service account permissions to make sure it can deploy to the cluster.
