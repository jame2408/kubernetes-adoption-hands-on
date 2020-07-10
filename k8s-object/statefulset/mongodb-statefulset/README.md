### Set up cluster role

```
$ kubectl create clusterrolebinding --user system:serviceaccount:default:default mongodb-cluster-admin --clusterrole cluster-admin
```