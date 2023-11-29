# Usage

```sh
minikube start --network-plugin=cni --cni=calico --kubernetes-version=v1.27.4
# Prometheus
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/prometheus
kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-np
kubectl get svc
# Grafana
helm repo add grafana https://grafana.github.io/helm-charts
helm install grafana grafana/grafana
kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-np
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

The following must be modified before `kustomize build`.
- [Artifact Repository](https://github.com/panicboat/minikube/blob/main/argo-workflows/applications/default/configmap/artifact-repositories.yaml)
- [SLACK_WEBHOOK_URL](https://github.com/panicboat/minikube/blob/main/argo-workflows/applications/default/secret/slack-config.yaml)
- [AWS CREDENTIAL](https://github.com/panicboat/minikube/blob/main/argo-workflows/applications/default/secret/aws-credentials.yaml)

```sh
kustomize build . | kubectl apply -f -
```

[Prometheus and Grafana setup in Minikube](https://brain2life.hashnode.dev/prometheus-and-grafana-setup-in-minikube)

## Prometheus

```sh
minikube service prometheus-server-np --url
```

## Grafana

```sh
minikube service grafana-np --url
```

## Argo Workflows

```sh
kubectl -n argo port-forward deployment/argo-server 2746:2746
```
https://127.0.0.1:2746

### Metrics

```sh
kubectl -n argo port-forward deploy/workflow-controller 9090:9090
```
https://127.0.0.1:9090/metrics
