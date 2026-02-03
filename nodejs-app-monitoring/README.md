# Monitoring NodeJs App

##### Metrics
The project exposes metrics on /metrics endpoint

To run the nodejs application:

    npm install 
    node app/server.js

To build the project:

    docker build -t repo-name/image-name:image-tag .
    docker push repo-name/image-name:image-tag

##### After adding Prometheus Client Library for Nodejs to your app

First, add Prometheus Monitoring Stack Operator (as a helm chart):

    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    kubectl create namespace monitoring
    helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring

Create a secret for your dockerHub credentials:

    kubectl create secret docker-registry my-registry-key --docker-server=https://index.docker.io/v1/ --docker-username=username --docker-password=xxxxxx

Then apply deployment, service and ServiceMonitor:

    kubectl apply -f k8s-config.yaml

Forward your services:

    Start-Process kubectl -ArgumentList "port-forward service/nodeapp 3000:3000"
    Start-Process kubectl -ArgumentList "port-forward service/monitoring-kube-prometheus-prometheus -n monitoring 9090:9090" 
    Start-Process kubectl -ArgumentList "port-forward service/monitoring-grafana -n monitoring 80:80"

Get the Grafana username(admin) and password:

     kubectl get secret monitoring-grafana -n monitoring -o jsonpath="{.data.admin-password}" | % { [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($_)) }

Test the endpoint:

    1..1000 | % { iwr http://127.0.0.1:3000 -UseBasicParsing | Out-Null }