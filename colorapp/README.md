# Add new charts into this repository
This file shows the steps to add new chart into this repository

## Create new helm chart
To create new helm chart create its structure via simple helm command. Make sure you have installed helm. 
[Install Helm](https://helm.sh/docs/intro/install/)
```
helm create colorapp
```

## Test Helm chart
Deploy locally to test if its workign as expected
### Deploy using command line paramaters
```
helm install local-colorapp ./colorapp \
  --set appVersion=v1.0.0 \
  --set service.type=ClusterIP \
  --set service.port=8080 \
  --set replicaCount=2 \
  --set hosts.host=colorapp.example.com \
  --set ingress.annotations."alb\\.ingress\\.kubernetes\\.io/load-balancer-name"="colorapp-alb"

```
### Deploy using command values file
Copy values.yaml file as my_values.yaml. Modify all the required values and add additional annotations if needed.
```
helm install local-colorapp ./colorapp -f my_values.yaml
```

To upgrade
```
helm upgrade local-colorapp ./colorapp
```

To delete
```
helm uninstall local-colorapp
```

## Deploy helm chart
There are multiple ways to deply the delm charts and passed additional configuration pramaters while deploying. For example additional ingress annotation can be provided while deploying this helm chart. [Supported Annotations](https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.2/guide/ingress/annotations/#annotations)
### From command line

#### Add the Repo to Helm
On any machine, you can now add your repo:
```
helm repo add chartshelf https://robokingmaster.github.io/chart-shelf/
helm repo update

helm repo list
helm search repo chartshelf

$ helm search repo chartshelf
NAME                    CHART VERSION   APP VERSION     DESCRIPTION                                      
chartshelf/colorapp     1.0.0           1.0.0           A Helm chart for deploying colorapp in Kubernetes
$ 
```

### Install Helm Charts
Retrive the values file and update accordingly. 

#### Install using values file
```
helm inspect values chartshelf/colorapp > colorapp.yaml
```
We can also add ACM certificate in annotation as or provide the TLS certificate
```
replicaCount: 1

appenv:
  appcolor: red

image:
  repository: robokingmaster/examples
  tag: colorapp
  pullPolicy: IfNotPresent

service:
  type: NodePort
  port: 80

ingress:
  enabled: true
  className: alb
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/healthcheck-path: /health
    alb.ingress.kubernetes.io/group.name: frontend
    alb.ingress.kubernetes.io/certificate-arn: <ACM ARN Endpoint>
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTPS": 443}]'
  hosts:
    - host: colorapp.example.com
      paths:
        - path: /
          pathType: Prefix
  tls: []
```
Using this values file lets install the helm chart
```
helm install colorapp chartshelf/colorapp --namespace colorapp --values colorapp.yaml
```

#### Install using command line paramaters
```
helm install local-colorapp ./colorapp \
  --set appVersion=v1.0.0 \
  --set service.type=ClusterIP \
  --set service.port=8080 \
  --set replicaCount=2 \
  --set hosts.host=colorapp.example.com \
  --set ingress.annotations."alb\\.ingress\\.kubernetes\\.io/load-balancer-name"="colorapp-alb"

```

### Uninstall Chart
```
helm uninstall colorapp --namespace colorapp
helm repo remove chartshelf
```
### Deploy using terraform
Terraform resource to deploy this chart
```
resource "helm_release" "color_app" {
  name       = "colorapp"
  repository = "https://robokingmaster.github.io/chart-shelf"
  chart      = "colorapp"  

  set {
    name  = "replicaCount:"
    value = "1"
  }

  set {
    name  = "hosts.host:"
    value = "<Your fully qualified domain name>"
  }

  // Optional - To Enable TLS
  set {
    name  = "ingress.annotations.alb\\.ingress\\.kubernetes\\.io/certificate-arn"
    value = "<Your ACM certificate arn>"
  } 

  // Optional - If your cluster has alb_controller deployed then this will provision ALB"
  set {
    name  = "ingress.annotations.alb\\.ingress\\.kubernetes\\.io/load-balancer-name"
    value = "<Provided ALB name will be used to provision alb>"
  }   

  // Optional - If you already have ALB"
  set {
    name  = "ingress.annotations.alb\\.ingress\\.kubernetes\\.io/group.name"
    value = "<Your group name for ALB>"
  } 

  // Optional - Retrict Inbound Trafic"
  set {
    name  = "ingress.annotations.alb\\.ingress\\.kubernetes\\.io/inbound-cidrs"
    value = "<Comma [,] Seperated cird ranges >"
  }     
}
```