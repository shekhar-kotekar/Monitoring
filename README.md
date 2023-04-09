# Purpose of this repository
Purpose of this repository is learn and play around with different aspects of application and infrastructure monitoring and observability.

Technologies which we are planning to use are:
- Grafana and Prometheus for observability
- Kafka and RedPanda to store streaming data
- Spark, Kafka Streams and Apache Ballista / Data Fusion to process data
- Rust to read network packets and send this data to Kafka / RedPanda


By using this technology stack we are aiming to learn following:
- Rust programming
- Adding application monitoring code
- Create beautiful dashboards to view application performance
- Observe and compare RedPanda / Kafka performance under different load

## How to get started?
- Execute shell script to start both Grafana and Prometheus.
- Execute `docker inspect <Prometheus container name or ID> | grep IPAddress` and note down IP address
- Prometheus is available at http://localhost:9090/
- Grafana can be accessed on http://localhost:3000/
    - credentials
        - user name: admin
        - password: admin
    - Grafana will prompt to update password. Update password and remember it. We can reuse same password.
    - Once inside Grafana, Goto Configuration -> Data Sources -> Add Data Source -> Prometheus
    - In HTTP -> URL section, provide `http://<IP address of Prometheus container>:9090` where IP address is noted in the steps above.

## How to use Kubernets installed in local (using Docker Desktop)?
- Execute `kubectl config use-context docker-desktop` command where
    - `docker-desktop` is the name of the context which is installed by Docker Desktop
- All the Kubernetes contexts can be listed by executing `kubectl config view | more` command

## How to monitor local Kubernetes cluster using Prometheus and Grafana?
- Get the list of existing namespaces in Kubernetes
```
$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   55d
kube-node-lease   Active   55d
kube-public       Active   55d
kube-system       Active   55d
```

- Now we have to create a new namespace to reserve a separate place for Grafana and Prometheus
```
$ kubectl create namespace kubernetes-monitoring
namespace/kubernetes-monitoring created

$ kubectl get namespaces                        
NAME                    STATUS   AGE
default                 Active   55d
kube-node-lease         Active   55d
kube-public             Active   55d
kube-system             Active   55d
kubernetes-monitoring   Active   3s
```

We will use readymade Helm charts for Prometheus and Grafana to save some time AND
because we are new to Kubernetes, Grafana, Prometheus and many other technologies.

## How to start Prometheus in local Kubernetes cluster?
```
# Add and install / update Prometheus Helm chart 
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus


# Get the Prometheus server URL by running these commands in the same shell:
export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace default port-forward $POD_NAME 9090
```

## How to start Grafana in local Kubernetes cluster?
```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install my-release grafana/grafana

# Get your 'admin' user password by running:
kubectl get secret --namespace default my-release-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

# Get the Grafana URL to visit by running these commands in the same shell:
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=my-release" -o jsonpath="{.items[0].metadata.name}")

kubectl --namespace default port-forward $POD_NAME 3000
```

## How to connect Grafana to Prometheus?
We need to add Prometheus as a data source in Grafana so that we can query and build beautiful dashboards in Grafana.

### Note down IP address of Prometheus pod
```
echo $(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl describe pod prometheus-server-<some random numbers> | grep IP
```

### Add Prometheus as a data source
1. Open local Grafana by going to `http://localhost:3000/datasources`
2. Click on "Add a new Data source" -> "Prometheus"
3. Write `http://<PROMETHEUS POD IP ADDRESS>:9090` in HTTP -> URL section
4. Click on `Save & test`. If everything is running smoothly then we will get message in green confirming that the data source is added.