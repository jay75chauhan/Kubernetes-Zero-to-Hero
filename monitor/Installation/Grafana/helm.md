# Install using Helm

## Add helm repo

`helm repo add grafana https://grafana.github.io/helm-charts`

## Update helm repo

`helm repo update`

## Install helm

`helm install grafana grafana/grafana`

## Expose Grafana Service

`kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext`

## admin password

` kubectl get secret grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo`

## dashboard import id- 3662
