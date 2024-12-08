

- helm env --> retrieve client information
- --debug --> flag to enable verbose output


## Helm Components:
- yaml files with plachoders
- values.yaml --> file with values for the placheolders
- chart.yaml --> info about the chart , what it is and the creator,..
- helm search hub wordpress --> search from artifact hub
- helm repo add bitnami bitnami_url
- helm repo list
- helm search repo wordpress 
- helm install release_name chart_name
- helm list
- helm uninstall release_name
- helm pull --untar bitnami/wordpress
- After instaling ,can change the values.yaml then install using `helm install name path` 

Diff between chart and app version