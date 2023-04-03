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