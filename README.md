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