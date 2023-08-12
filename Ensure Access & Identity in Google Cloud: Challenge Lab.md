# Ensure Access & Identity in Google Cloud: Challenge Lab
- Custom IAM Security Role: orca_storage_manager_925
- Service Account: orca-private-cluster-152-sa
- Cluster: orca-cluster-161
- Remember to replace the name with {} using lab given name

## Task 1: Create a custom security role.
```yaml
gcloud config set compute/zone us-east1-b
nano role-definition.yaml

title: "{orca_storage_manager_925}"
description: "Permissions"
stage: "ALPHA"
includedPermissions:
  - storage.buckets.get
  - storage.objects.get
  - storage.objects.list
  - storage.objects.update
  - storage.objects.create

gcloud iam roles create {orca_storage_manager_925} --project $DEVSHELL_PROJECT_ID \
--file role-definition.yaml
```

## Task 2: Create a service account.
```yaml
gcloud iam service-accounts create {orca-private-cluster-152-sa} --display-name "Orca Private Cluster Service Account"
```

## Task 3: Bind a custom security role to a service account.
```yaml
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID
--member serviceAccount:{orca-private-cluster-152-sa}@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com
--role roles/monitoring.viewer

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID
--member serviceAccount:{orca-private-cluster-152-sa}@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com
--role roles/monitoring.metricWriter

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID
--member serviceAccount:{orca-private-cluster-152-sa}@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com
--role roles/logging.logWriter

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID
--member serviceAccount:{orca-private-cluster-152-sa}@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com
--role projects/$DEVSHELL_PROJECT_ID/roles/{orca_storage_manager_925}
```

## Task 4: Create and configure a new Kubernetes Engine private cluster
```yaml
gcloud container clusters create {orca-cluster-161} --num-nodes 1 --master-ipv4-cidr=172.16.0.64/28
--network orca-build-vpc --subnetwork orca-build-subnet --enable-master-authorized-networks
--master-authorized-networks 192.168.10.2/32 --enable-ip-alias --enable-private-nodes --enable-private-endpoint
--service-account {orca-private-cluster-152-sa}@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --zone us-east1-b
```
## Task 5: Deploy an application to a private Kubernetes Engine cluster.
- Navigation Menu > Compute Engine > VM instances
- Click SSH button of orca-jumphost instance

```yaml
gcloud config set compute/zone us-east1-b
gcloud container clusters get-credentials orca-cluster-161 --internal-ip

#If failed, install plugin below
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
kubectl expose deployment hello-server --name orca-hello-service --type LoadBalancer --port 80 --target-port 8080
```

### gke-gcloud-auth-plugin
- Only use this codes if failed to create deployment using kubectl
```yaml
sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin
echo "export USE_GKE_GCLOUD_AUTH_PLUGIN=True" >> ~/.bashrc
source ~/.bashrc
gcloud container clusters get-credentials <your cluster name> --internal-ip --project=<project ID> --zone <cluster zone>
```

