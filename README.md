# vpa
The VPA - Vertical Pod Autoscaler - can make recommendations regarding the tuning of resource requests and limits.
Note that it is NOT YET production ready, so it is deployed in the "off" update mode, where VPA recommender analyzes resource usage and generates recommendations, but these recommendations are not automatically applied to Pods. The recommendations are only stored in the VPA object's .status field, which can be viewed using a tool such as kubectl or k9s.

You can pull the chart from its official repo:
```bash
helm repo add autoscalers https://kubernetes.github.io/autoscaler
helm repo update
helm upgrade -i vertical-pod-autoscaler autoscalers/vertical-pod-autoscaler
```
This repo contains a parent helm chart, and is initialized with: 
```bash
mkdir k8s-vpa
cd k8s-vpa
helm create helm
rm -rf helm/templates/*
```
Edit helm/Chart.yaml:
```Bash
apiVersion: v2
name: vpa
description: A Helm chart for vpa on Kubernetes
type: application
version: 0.1.0
appVersion: "1.6.0"

dependencies:
  - name: vertical-pod-autoscaler
    version: 0.8.1
    repository: https://kubernetes.github.io/autoscaler
```
Run the dependency update which will download the vpa helm chart in tgz format and place it in the charts sub-folder as a child helm chart. It will also create the Chart.lock file.
```Bash
cd helm
helm dependency update
cd charts
tar xvf *tgz
rm -f *tgz
```
In the parent helm chart values.yaml file (k8s-vpa/helm/values.yaml), you can override settings from the child helm chart (k8s-vpa/helm/charts/vertical-pod-autoscaler/values.yaml). Copy the values.yaml file from the child helm chart to the parent helm chart, and modify/clean up where necessary.

Test deploy the helm chart - from the helm folder:
```Bash
helm -n kube-system install vpa . -f values.yaml
```
