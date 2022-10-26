
## Prerequisites 
For this to work, there are a few things that are needed.  In our case, idm-user-sync-kafka fulfills all of these things (but can be applied to any service you desire custom metrics for)
* k8s cluster
* k8s deployment/service exposing prometheus metrics on /metrics
* service monitor that (makes k8s service discoverable by prometheus) 
* Optional - hpa that uses the custom metrics.  It is not needed but not sure why this would be done in the first place if you are not using one (whole purpose is to be able to access prometheus metrics back in k8s)

## Steps to enable/deploy
0. Create monitoring namespace with appropriate label by applying 0-namespaces
1. Add following label to the namespace that you are monitoring (in our case it is idm):
```
  labels:
    monitoring: prometheus
```
2. Apply 1-prometheus-operator-crd
3. Apply 2-prometheus-operator
4. Apply 3-prometheus
5. Apply 4-your-service after you fill out information for your service (in our case was idm-user-sync-kafka service, deployment, hpa, and servicemonitor)
6. Apply 5-prometheus-adapter/0-adapter - for this step, you may want to use different metrics to be used by your hpa.  See [here](https://github.com/kubernetes-sigs/prometheus-adapter/blob/master/docs/config.md) for more details on configmap.  Also make sure the configmap metric you are using matches your hpa. 
7. Apply 5-prometheus-adapter/1-custom-metrics

If you want to look at metrics in this local deployment and you're using minikube, I would expose the prometheus service created in the previous steps:
```
kubectl expose service prometheus-operated --type=NodePort --target-port=9090 --name=prometheus-operated-np -n monitoring
```
Then you can check what service is created, use your minikube ip + exposed port to see prometheus metrics on your local minikube cluster. 

Example - if the output of the two commands is the following, then I would visit `192.168.59.106:31689` to see prometheus metrics:
```
$ minikube ip
192.168.59.106
$ kubectl get svc -n monitoring
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
prometheus-operated      ClusterIP   None           <none>        9090/TCP         8m23s
prometheus-operated-np   NodePort    10.105.19.37   <none>        9090:31689/TCP   3s
prometheus-operator      ClusterIP   None           <none>        8080/TCP         8m36s
```
