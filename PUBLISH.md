# helm-charts
Helm charts for quickly deploying sample applications

### Publishing charts in github

#### Prepare Your Helm Charts
Organize your charts in a directory structure like this:
```
chart-shelf/
├── charts/
│   ├── colorapp/
│   └── nginx/
```
Each chart folder should follow the standard Helm chart structure (Chart.yaml, values.yaml, templates/, etc.).

#### Package the Charts
Use the Helm CLI to package each chart:

```
helm package colorapp
helm package nginx
```
This will generate .tgz files like colorapp-1.0.0.tgz.

#### Create an index.yaml File
Generate the Helm repository index file:
```
helm repo index . --url https://robokingmaster.github.io/chart-shelf
```

### Commit and push
```
git add all
git commit -m "Add packaged charts and index"
git push origin main
```

#### Enable GitHub Pages
Go to your GitHub repository settings:

Navigate to Pages.
Set the source to the gh-pages branch and root directory.
GitHub will provide a URL like:
https://<your-username>.github.io/<repo-name>/

#### Add the Repo to Helm
On any machine, you can now add your repo:
```
helm repo add myrepo https://<your-username>.github.io/<repo-name>/
helm repo update

helm search repo myrepo
```

#### Install Charts
Retrive the values file and update accordingly. 

```
helm inspect values myrepo/colorapp > colorapp.yaml
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
helm install colorapp myrepo/colorapp --namespace colorapp --values colorapp.yaml
```

#### Uninstall Chart
```
helm uninstall colorapp --namespace colorapp
helm repo remove myrepo
```