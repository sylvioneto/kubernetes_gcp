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

2. Ensure the Cloud Build API is enabled and it has `roles/container.admin`.
```
gcloud services enable cloudbuild.googleapis.com
gcloud projects add-iam-policy-binding $GCP_PROJECT_ID \
    --member="serviceAccount:$GCP_PROJECT_NUMBER@cloudbuild.gserviceaccount.com" \
    --role='roles/container.admin'
```

3. Deploy NGINX with Cloud Build informing the cluster's name and region, for example:
```
gcloud builds submit . \
  --config cloudbuild.yaml \
  --substitutions="_GKE_REGION=us-central1","_GKE_CLUSTER=gke-dev"
```

## Known-issues
- Access Denied: If you get access denied during the deployment, please review Cloud Build's service account permissions to make sure it can deploy to the cluster.
- Service webhook failing: your cluster is private it is probably missing master to node firewall rule ([ref](https://kubernetes.github.io/ingress-nginx/deploy/#gce-gke)).
