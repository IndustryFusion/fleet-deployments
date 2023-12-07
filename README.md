# IFF OPC-UA data service deployments with Akri OPC-UA Discovery-Handler

**Folder - akri:**

OPC-UA (Open Platform Communications Unified Architecture) is a communication protocol for industrial automation. Akri has implemented a Discovery Handler for discovering OPC UA Servers that live at specified endpoints or are registered with specified Local Discovery Servers.

The akri folder contains fleet configuration to deploy Akri components and OPC-UA discovery handler. The fleet config file internally uses Akri Helm charts for the deployment. Akri is responsible for deploying IFF OPC-UA data broker service stack upon discovery of a specified server endpoint (More on this below).

**Folder - iff:**

IFF broker service Helm chart is located in this folder. Using values.yaml file, the IFF broker service Helm template can be configured.

IFF OPCUA data broker service/pod contains two containers, one is the fusionopcuadataservice which is a Python client for fetching data from OPC UA servers and another is the PDT agent (Process Data Twin) for transferring the fetched data to the PDT backend.

The IFF broker containers are deployed under a single broker pod. The broker pod's lifecycle management is handled by Akri. The broker pod also needs extra resource configurations such as secrets, configmaps, etc that are also part of the deployment file.

**Discovery Handler (DH) Configuration:**

The Akri's DH must be given a set of URLs and the OPC-UA servers's applicationName value to discover the appropriate machine. These are passed to Akri with IFF broker's Helm template as part of Akri 'Configuration' custom resource.

IFF uses a single node for each machine, and each machine will have its own OPC configuration data tree. These configurations are deployed as configmaps as described above. Because of the single node, the applicationName config for Akri will contain only one server name.

**Deployment:**

Step 1: Go to akri/fleet.yaml, and update the 'kubernetesDistro' value to match the Kubernetes version. K3s for K3S and RKE2 distributions.

Step 2: Go to iff/templates/iff-broker-service.yaml, update the placeholders (<>) accordingly.

Step 3: Go to iff/templates/oisp-token-operator.yaml, update the placeholders (<>) accordingly. 

Note: OISP-token-operator service will be deprecated soon. Till then, build the docker image for the service here - [https://github.com/IndustryFusion/oisp-token-operator/blob/main/Dockerfile]

Step 4: Go to iff/values.yaml, update the placeholders (<>) accordingly.

The helm charts in this project are desgined to be deployed using Rancher's Fleet plugin (GitOps based Continous Delivery plugin). In the Rancher Management Server, navigate to 'Continous Delivery' option from the left navigation bar. Add a new Git Repo, mention name of the repository and branch that contains this project, deploy it to the desired cluster in Rancher.

Note: The charts can also be used to deploy on Kubernetes using Helm CLI with minor modification.

**Troubleshooting/Known Issues**

1. All IP addresses in the subnet must be listed with possible ports, which is a huge list of combinations. The Local Discovery Server (LDS) proposal from the OPC foundation might help to aggregate all the OPC-UA servers in one place. (Reaserch and Testing - In Progress) [https://profiles.opcfoundation.org/profile/1634]
2. Some machines have their OPC UA server port at 4840 (standard), and some at 62548 (custom).
3. The OPC server's application name value is not unique across locations and clients. It might be a localized value. (Akri must be restructured to use applicationUri instead - SUSE has taken note)

