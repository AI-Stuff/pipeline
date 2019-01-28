# Install [PipelineAI CLI](../README.md#install-pipelinecli)
* Click [**HERE**](../README.md#install-pipelinecli) to install the PipelineAI CLI

### Prerequisites
* Running Kubernetes Cluster

### Pull, Tag, and Push PipelineAI Docker Images
_Email [contact@pipeline.ai](mailto:contact@pipeline.ai) to request access to the PipelineAI Docker Repo._

```
pipeline _env_registry_fullsync --tag=1.5.0 --chip=cpu

pipeline _env_registry_fulltag --from-image-registry-url=954636985443.dkr.ecr.us-west-2.amazonaws.com \
                               --from-image-registry-repo=pipelineai \
                               --from-tag=1.5.0 \
                               --to-image-registry-url=<your-docker-repo-url> \
                               --to-image-registry-repo=pipelineai \
                               --to-tag=1.5.0 \
                               --chip=cpu

pipeline _env_registry_fullpush --image-registry-url=<your-docker-repo-url> \
                                --image-registry-repo=pipelineai \
                                --tag=1.5.0 \
                                --chip=cpu
```

### IAM Roles
Make sure the underlying EC2 instances for your EKS cluster contain the `AmazonEC2ContainerRegistryPowerUser` instance policy.   See [here](https://aws.amazon.com/blogs/security/easily-replace-or-attach-an-iam-role-to-an-existing-ec2-instance-by-using-the-ec2-console/) and [here](https://eksworkshop.com/logging/prereqs/) for more info.

### Choose a node as an "admin" node.
```
kubectl get node

NAME                                          STATUS    ROLES     AGE       VERSION
<node1>                                       Ready     <none>    24m       v1.10.3
<node2>                                       Ready     <none>    24m       v1.10.3
```

### Create the cluster by specifying the admin node from above
* Requires `cli-pipeline>=1.5.260`.  Click [here](https://github.com/PipelineAI/pipeline/blob/master/docs/quickstart/README.md#install-pipelinecli) to install the PipelineAI CLI.
```
pipeline cluster_kube_install --tag 1.5.0 --admin-node <node1-or-node2> --image-registry-url <your-docker-repo-url>
```
Notes:  
* If you see logs of `Evicted` or `Pending` nodes, you may need to increase the instance size (memory and cpu) and/or increase the capacity of your EBS volumes.  Use `kubectl describe pod <Evicted-or-Pending-pod-name>` to identify the underlying issue.


### Retrieve the ELB DNS name
Note: the ELB DNS may take some time to propagate.
```
kubectl get svc istio-ingressgateway -o wide

NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP  
istio-ingressgateway   LoadBalancer   10.100.12.101   <dns-name>  
```

### Whitelist the DNS Name with PipelineAI
_Email [contact@pipeline.ai](mailto:contact@pipeline.ai) to whitelist your DNS name with PipelineAI._

