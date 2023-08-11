# Perform Foundational Infrastructure Tasks in Google Cloud: Challenge Lab
- Bucket Name, Topic Name, Cloud Function Name is given

## Task 1: Create a bucket
- Navigation Menu > Cloud Storage > Buckets > Create
- Bucket name: Lab given > Continue
- Location Type: Region: us-east-1 
- Leave others as default > Create

## Task 2: Create a Pub/Sub topic
- Navigation menu > Pub/Sub > Topics > Create Topic
- Topic ID: Lab given > Create

## Task 3: Create the thumbnail Cloud Function
- Navigation menu > Cloud Functions > Create Function
- Function name: Lab given
- Region: us-east1 
- Trigger type: Cloud Storage 
- Event type: Finalizing/Creating 
- Bucket: BROWSE > Select the created bucket > Next
- Runtime: Node.js 14 Entry point: thumbnail
- Copied and paste the code of index.js and package.js > Deployed
- Use any image or Download the given image: https://storage.googleapis.com/cloud-training/gsp315/map.jpg
- Navigation menu > Cloud Storage > Buckets > Select the created bucket > Upload files > Upload the image

## Task 4: Remove the previous cloud engineer
- Navigation menu > IAM & Admin > IAM
- Find the Principal of username2 > Edit Principal > Delete Role > Save 
