# How to Setup Huble with Prometheus

## Setup kubectl to connect to GKE


`gcloud config set project <Your GCP Project>`
`gcloud config set compute/zone us-east4-b`

`gcloud container clusters get-credentials onlineboutique`

`kubectl get pods -A`

##Install and configure Helm
`brew install helm`

`helm repo add cilium https://helm.cilium.io/`

Determine the native CIDR
Run the following command
 `gcloud container clusters describe onlineboutique --format 'value(clusterIpv4Cidr)'`

then update config.yml in this repo
nativeRoutingCIDR: <the value of command above>


## Install Cilium
helm install cilium cilium/cilium --version 1.9.7 --namespace kube-system -f config.yml

## Setup Monitoring stack (Grafana/Prometheus)
`apply -f monitoring.yml`

## Apply Cilium Network Policies
For HTTP
`kubectl apply -f http-policy.yml`
For DNS
`kubectl apply -f dns-policy.yml`


## Setup Port Fordwarding via kubectl to access UI's

### Hubble UI Access
`kubectl port-forward -n kube-system svc/hubble-ui 12000:80`

### Grafana Access
`kubectl -n cilium-monitoring port-forward service/grafana --address 0.0.0.0 --address :: 3000:3000`

### Prometheus Access
`kubectl -n cilium-monitoring port-forward service/prometheus --address 0.0.0.0 --address :: 9090:9090`



## Metrics Notes:
To modify the lables in the promethes metrics section in the config.yml
See this https://docs.cilium.io/en/latest/operations/metrics/#hubble-context-options
