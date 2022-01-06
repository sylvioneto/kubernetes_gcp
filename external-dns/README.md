# External DNS on GKE

## Description

This example demonstrates how to deploy [External DNS](https://github.com/kubernetes-sigs/external-dns) on GKE.

## Pre-req:
You must have a GKE cluster ready to received deployments. In case you don't have it, you can create one using [this example](https://github.com/sylvioneto/terraform_gcp/tree/master/gke).


## Deploy
1. Ensure your gcloud is set to the right project.
````
gcloud config list
````

2. Set the GCP_PROJECT_ID env var
````
export GCP_PROJECT_ID=<project-id>
````

3. Create a service account for External DNS:
````
gcloud iam service-accounts create external-dns --display-name="External DNS service account"
````

4. Assign deployment permissions to Jenkins according to the pipelines it'll run, example:
````
gcloud projects add-iam-policy-binding $GCP_PROJECT_ID --member="serviceAccount:external-dns@$GCP_PROJECT_ID.iam.gserviceaccount.com" --role='roles/dns.admin'
gcloud projects add-iam-policy-binding $GCP_PROJECT_ID --member="serviceAccount:external-dns@$GCP_PROJECT_ID.iam.gserviceaccount.com" --role='roles/container.viewer'
````

5. [Workload Identity](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity) - bind External's KSA to GSA
```
gcloud iam service-accounts add-iam-policy-binding external-dns@$GCP_PROJECT_ID.iam.gserviceaccount.com \
  --role roles/iam.workloadIdentityUser \
  --member "serviceAccount:$GCP_PROJECT_ID.svc.id.goog[external-dns/external-dns]"
```

6. Edit the [external-dns.yaml](./external-dns.yaml) file according to your DNS and project settings.

7. Deploy it informing the cluster's name and region, for example:
```
gcloud builds submit . \
  --config cloudbuild.yaml \
  --substitutions="_GKE_REGION=us-central1","_GKE_CLUSTER=gke-dev"
```

## Known-issues
Access Denied: If you get access denied during the deployment, please review Cloud Build's service account permissions to make sure it can deploy to the cluster.
