# MQTT Data service deployment using Kustomize 

This example will deploy the mqtt application
using kustomize. The app will be deployed into the `oisp-devices` namespace.

Visit all the configuration files and update the placeholders (<>) accordingly.

Note: OISP-token-operator service will be deprecated soon. Till then, build the docker image for the service here - [https://github.com/IndustryFusion/oisp-token-operator/blob/main/Dockerfile]

These Kustomize charts in this project are desgined to be deployed using Rancher's Fleet plugin (GitOps based Continous Delivery plugin). In the Rancher Management Server, navigate to 'Continous Delivery' option from the left navigation bar. Add a new Git Repo, mention name of the repository and branch that contains this project, deploy it to the desired cluster in Rancher.

Note: The charts can also be used to deploy on Kubernetes using Helm CLI with minor modification.