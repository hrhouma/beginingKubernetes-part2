# Flux des données et les dépendances :

```
+--------------------------------------------------------------+
|                          Kubernetes                          |
+--------------------------------------------------------------+
|                                                              |
|  +----------------+       +---------------------+            |
|  | kube-state-    |       | grafana-configmap   |            |
|  | metrics-       +------>+ & grafana-deployment+----+       |
|  | service-       |       | & grafana-service   |    |       |
|  | account.yaml   |       +----------^----------+    |       |
|  +--------^-------+                  |               |       |
|           |                          |               |       |
|  +--------+---------+       +--------+---------+     |       |
|  | kube-state-      |       | metrics-server-   |     |       |
|  | metrics-cluster- +<------+ prometheus.      |     |       |
|  | role.yaml &      |       | deployment.yml   |     |       |
|  | kube-state-      |       +--------^---------+     |       |
|  | metrics-cluster- |                |               |       |
|  | role-binding.yaml|                |               |       |
|  +--------^---------+                |               |       |
|           |                          |               |       |
|  +--------+---------+       +--------+--------+      |       |
|  | kube-state-      |       | Web-UI-dashboard-|      |       |
|  | metrics-         |       | adminuser.yml    |      |       |
|  | deployment.yaml  |       +--------^--------+      |       |
|  | & kube-state-    |                |               |       |
|  | metrics-service  |                |               |       |
|  | .yaml            |                |               |       |
|  +--------^---------+       +--------+--------+      |       |
|           |                | Web-UI-newDeploy |      |       |
|           |                | .yml             |      |       |
|           |                +--------^---------+      |       |
|           |                          |               |       |
|           |                          |               |       |
|           +--------------------------+---------------+       |
|                                                              |
+--------------------------------------------------------------+
```

