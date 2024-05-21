# Manage Kubernetes in Google Cloud: Challenge Lab
## Note: this tutorial is not followed the task order
1. Google Cloud Console > Search Log Based Metric > Create Metric
2. Metric type: Counter
3. Log Metric Name : pod-image-errors
4. Build filter
```yaml
resource.type="k8s_pod"
severity=WARNING
```
5. Create Metric
6. Get value from labs
## Copy below codes to notepad for further steps
```yaml
export REPO_NAME=
export CLUSTER_NAME=
export ZONE=
export NAMESPACE=
export INTERVAL=
export SERVICE_NAME=
```
Get the value from labs
Example:
```
export REPO_NAME=sandbox-repo <Get from challenge scenario>
export CLUSTER_NAME=hello-world-57l5 <Get from Task 1>
export ZONE=us-central1-c <Get from Task 1>
export NAMESPACE=gmp-lger <Get from Task 2.2>
export INTERVAL=30s <Get from Task 2.7>
export SERVICE_NAME=helloweb-service-v4st <Get from Task 6.6>
```
7. Open Cloudshell and paste your completed codes above
8. Paste the codes below
```yaml
curl -LO raw.githubusercontent.com/quiccklabs/Labs_solutions/master/NEW%20Manage%20Kubernetes%20in%20Google%20Cloud%20Challenge%20Lab/quicklabgsp510.sh
sudo chmod +x quicklabgsp510.sh
./quicklabgsp510.sh
```
9. Wait til the code is done

Back to Google Lab, click the Marks tab to check progress. You completed the lab!

