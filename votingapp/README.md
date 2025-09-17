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
### Deploy using command line paramaters
```
helm install local-votingapp ./votingapp

```
### Deploy using command values file
Copy values.yaml file as my_values.yaml. Modify all the required values and add additional annotations if needed.
```
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