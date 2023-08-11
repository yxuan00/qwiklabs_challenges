# Implement DevOps in Google Cloud: Challenge Lab with 6 tasks
- Email: username
- Name: student
  
## Task 1: Create the lab resources
```yaml
gcloud services enable container.googleapis.com \
    cloudbuild.googleapis.com \
    sourcerepo.googleapis.com

export PROJECT_ID=$(gcloud config get-value project)
gcloud projects add-iam-policy-binding $PROJECT_ID \
--member=serviceAccount:$(gcloud projects describe $PROJECT_ID \
--format="value(projectNumber)")@cloudbuild.gserviceaccount.com --role="roles/container.developer"

#Replace the email with username and name with student
git config --global user.email <email> 
git config --global user.name <name>

#Remember to replace the project_id
export PROJECT_ID= <project_id>
export CLUSTER_NAME=hello-cluster
export ZONE=us-central1-a
export REGION=us-central1
export REPO=my-repository

gcloud artifacts repositories create $REPO \
 --repository-format=docker \
 --location=$REGION \
 --description="Subscribe to quicklab"

#if error because unsupported version, try 1.25.11-gke.1700 or newer
gcloud beta container --project "$PROJECT_ID" clusters create "$CLUSTER_NAME" --zone "$ZONE" --no-enable-basic-auth --cluster-version "1.25.5-gke.2000"
--release-channel "regular" --machine-type "e2-medium" --image-type "COS_CONTAINERD" --disk-type "pd-balanced" --disk-size "100"
--metadata disable-legacy-endpoints=true --logging=SYSTEM,WORKLOAD --monitoring=SYSTEM --enable-ip-alias
--network "projects/$PROJECT_ID/global/networks/default" --subnetwork "projects/$PROJECT_ID/regions/$REGION/subnetworks/default"
--no-enable-intra-node-visibility --default-max-pods-per-node "110" --enable-autoscaling --min-nodes "2" --max-nodes "6" --location-policy "BALANCED"
--no-enable-master-authorized-networks --addons HorizontalPodAutoscaling,HttpLoadBalancing,GcePersistentDiskCsiDriver --enable-autoupgrade --enable-autorepair
--max-surge-upgrade 1 --max-unavailable-upgrade 0 --enable-shielded-nodes --node-locations "$ZONE" 

kubectl create namespace prod
kubectl create namespace dev
```

## Task 2: Create a repository in Cloud Source Repositories
```yaml
gcloud source repos create sample-app
git clone https://source.developers.google.com/p/$PROJECT_ID/r/sample-app

cd ~
gsutil cp -r gs://spls/gsp330/sample-app/* sample-app

git init
cd sample-app/
git add .
git commit -m "Subscribe to quicklab"
git push -u origin master

git branch dev
git checkout dev
git push -u origin dev
```

## Task 3: Create the Cloud Build Triggers
### First Trigger
- Navigation Menu > Cloud Build > Triggers > Create Trigger
- Name: sample-app-prod-deploy
- Event: Push to a branch
- Repository: sample-app
- Branch: ^master$
- Cloud Build Configuration File: cloudbuild.yaml
- Create

### Second Trigger
- Create Another Trigger > Create Trigger
- Name: sample-app-dev-deploy
- Event: Push to a branch
- Source Repository: sample-app
- Branch: ^dev$
- Cloud Build Configuration File: cloudbuild-dev.yaml
- Create

## Task 4: Deploy the first versions of the application
```yaml
COMMIT_ID="$(git rev-parse --short=7 HEAD)"
gcloud builds submit --tag="${REGION}-docker.pkg.dev/${PROJECT_ID}/$REPO/hello-cloudbuild:${COMMIT_ID}".
```

- Copied the IMAGES link

### Build the first development deployment
- Open Editor > cloudbuild-dev.yaml > Replace <version> with v1.0 in line 9 and 13 > Save
- dev > deployment.yaml > Replace <todo> with the copied IMAGES link > Save
- Back to Terminal
```yaml
git add .
git commit -m "Subscribe to quicklab"
git push -u origin dev

git checkout master
```

### Build the first production deployment
- Go to Editor > cloudbuild.yaml > Replace <version> with v1.0 in line 11 and 16 > Save
- prod > deployment.yaml > Replace <todo> with previous copied IMAGES link > Save
- Back to Terminal
```yaml
git add .
git commit -m "Subscribe to quicklab"
git push -u origin master
```

## Task 5: Deploy the second versions of the application
```yaml
git checkout dev
```
### Build the second development deployment
- Go to Editor > main.go > Update main function
```
func main() {
	http.HandleFunc("/blue", blueHandler)
	http.HandleFunc("/red", redHandler)
	http.ListenAndServe(":8080", nil)
}
```

- Add codes to main.go
```
func redHandler(w http.ResponseWriter, r *http.Request) {
	img := image.NewRGBA(image.Rect(0, 0, 100, 100))
	draw.Draw(img, img.Bounds(), &image.Uniform{color.RGBA{255, 0, 0, 255}}, image.ZP, draw.Src)
	w.Header().Set("Content-Type", "image/png")
	png.Encode(w, img)
}
```

- cloudbuild-dev.yaml > Replace previous <version> location with v2.0 on line 9 and 13
- dev > deployment.yaml > Change line 17 after hello-cloudbuild:v2.0
- Back to Terminal
```yaml
git add .
git commit -m "Subscribe to quicklab"
git push -u origin dev

git checkout master
```

### Build the second production deployment
- Go to Editor > main.go > Update main function
```
func main() {
	http.HandleFunc("/blue", blueHandler)
	http.HandleFunc("/red", redHandler)
	http.ListenAndServe(":8080", nil)
}
```

- Add codes
```
func redHandler(w http.ResponseWriter, r *http.Request) {
	img := image.NewRGBA(image.Rect(0, 0, 100, 100))
	draw.Draw(img, img.Bounds(), &image.Uniform{color.RGBA{255, 0, 0, 255}}, image.ZP, draw.Src)
	w.Header().Set("Content-Type", "image/png")
	png.Encode(w, img)
}
```

- cloudbuild.yaml > Replace previous <version> location on line 11 and 16 to v2.0
- prod > deployment.yaml > Change line 17 after hello-cloudbuild:v2.0
- Back to Terminal
```yaml
git add .
git commit -m "Subscribe to quicklab"
git push -u origin master
```

## Task 6: Roll back the production deployment
- Navigation Menu > Cloud Build > History
- Choose the second option > Rebuild
- Wait until it successful
