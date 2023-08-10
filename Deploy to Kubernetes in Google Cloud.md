# Deploy to Kubernetes in Google Cloud: Challenge Lab with 4 Tasks
 - Docker Image and Tag: valkyrie-app:v0.0.2
 - Repository: valkyrie-repo
 - Remember to change the name with {} using the given name of the lab 

## Task 1: Create a Docker image and store the Dockerfile
```yaml
source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking_v2.sh)
gcloud source repos clone valkyrie-app
cd valkyrie-app
cat > Dockerfile <<EOF
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
EOF
docker build -t {valkyrie-app:v0.0.2} .
cd ..
bash ~/marking/step1_v2.sh
```

## Task 2: Test the created Docker image
```yaml
cd valkyrie-app  #should in marking directory
docker run -p 8080:8080 {valkyrie-app:v0.0.2} &   #the website preview can be seen
click CTRL + C to stop
bash ~/marking/step2_v2.sh
```

## Task 3: Push the Docker image to the Artifact Registry
```yaml
gcloud artifacts repositories create {valkyrie-repo} \
    --repository-format=docker \
    --location=us-central1 \
    --description="subcribe to quciklab" \
    --async 
gcloud auth configure-docker us-central1-docker.pkg.dev
docker images
docker tag {valkyrie-app:v0.0.2} us-central1-docker.pkg.dev/$GOOGLE_CLOUD_PROJECT/{valkyrie-repo}/{valkyrie-app:v0.0.2}
docker push us-central1-docker.pkg.dev/$GOOGLE_CLOUD_PROJECT/{valkyrie-repo}/{valkyrie-app:v0.0.2}
```

## Task 4: Create and expose a deployment in Kubernetes
```yaml
sed -i s#IMAGE_HERE#us-central1-docker.pkg.dev/$GOOGLE_CLOUD_PROJECT/{valkyrie-repo}/{valkyrie-app:v0.0.2}#g k8s/deployment.yaml
gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d #dont need to change the zone
kubectl create -f k8s/deployment.yaml
kubectl create -f k8s/service.yaml
```
