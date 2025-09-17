# Add new charts into this repository
This file shows the steps to add new chart into this repository

## Create new helm chart
To create new helm chart create its structure via simple helm command. Make sure you have installed helm. 
[Install Helm](https://helm.sh/docs/intro/install/)
```
helm create votingapp
```
## Test Helm chart
Deploy locally to test if its workign as expected
### Deploy using command line with minimal parameters
```
helm install local-votingapp ./votingapp

```

### Deploy using command line paramaters and ingress
```
helm install local-votingapp ./votingapp \  
  --set vote.service.type=ClusterIP \
  --set vote.service.port=80 \
  --set vote.replicaCount=2 \
  --set vote.ingress.enabled=true \
  --set vote.hosts.host=votingapp.example.com \
  --set result.service.type=ClusterIP \
  --set result.service.port=80 \
  --set result.replicaCount=2 \
  --set result.ingress.enabled=true \
  --set result.host=votingresult.example.com 

```

### Deploy using command values file
Copy values.yaml file as my_values.yaml. Modify all the required values and add additional annotations if needed.

```
cp values.yaml my_values.yaml

helm install local-votingapp ./votingapp -f my_values.yaml
```

To upgrade
```
helm upgrade local-votingapp ./votingapp
```

To delete
```
helm uninstall local-votingapp
```