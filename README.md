
## Prerequisites 
For this to work, there are a few things that are needed.  In our case, idm-user-sync-kafka fulfills all of these things (but can be applied to any service you desire custom metrics for)
* k8s cluster
* k8s deployment/service exposing prometheus metrics on /metrics
* service monitor that (makes k8s service discoverable by prometheus) 
* Optional - hpa that uses the custom metrics.  It is not needed but not sure why this would be done in the first place if you are not using one (whole purpose is to be able to access prometheus metrics back in k8s)

## Steps to enable/deploy
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
