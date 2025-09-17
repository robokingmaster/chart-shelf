# helm-charts
Helm charts for quickly deploying sample applications

### Publishing charts in github

#### Prepare Your Helm Charts
Organize your charts in a directory structure like this:
```
chart-shelf/
├── charts/
│   ├── colorapp/
│   └── votingapp/
```
Each chart folder should follow the standard Helm chart structure (Chart.yaml, values.yaml, templates/, etc.).

#### Package the Charts
Use the Helm CLI to package each chart:

```
helm package colorapp
helm package votingapp
```
This will generate .tgz files like colorapp-1.0.0.tgz.

#### Create an index.yaml File
Generate the Helm repository index file:
```
helm repo index . --url https://robokingmaster.github.io/chart-shelf/
```

### Commit and push
```
git add --all
git commit -m "Add packaged charts and index"
git push origin main
```

#### Enable GitHub Pages
Go to your GitHub repository settings:

Navigate to Pages.
Set the source to the gh-pages branch and root directory.
GitHub will provide a URL like:
https://robokingmaster.github.io/chart-shelf/

