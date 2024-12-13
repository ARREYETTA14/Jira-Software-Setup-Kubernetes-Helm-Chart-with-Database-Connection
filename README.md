# Jira-Software-Setup-Kubernetes-Helm-Chart-with-Database-Connection

1. Set up an EKS cluster with the latest version, e.g., 1.31
2. Create a worker node in the cluster with at least t3.xlarge.
3. Create an EC2 instance that will act as the client node to communicate with the cluster.
4. SSH into the Client worker node, uninstall AWS version 1 and install AWS CLI version 2 such that you do not have API issues when trying to communicate with the cluster kubectl commands.
```bash
sudo yum remove awscli -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
ls -l /usr/local/bin/aws
```
- Check AWS Version after this
```sh
aws --version
```

5. Install the Kubectl version compatible with Cluster Version 1.31 by running all the required commands from the [AWS Documentation to install Kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html#linux_amd64_kubectl)
6. Check if your client node can now communicate with the cluster using:

```sh
kubectl version
or
kubectl get nodes
```
7. Install helm in the worker node
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
8. Verify helm installation
```sh
helm version
```

9. Add the ```stevehipwell``` Repo to the Helm 
```sh
helm repo add stevehipwell https://stevehipwell.github.io/helm-charts/
helm repo update
```

10. Update the “jira-values.yml” as per the requirement so it connects to your database.

***NB:*** By default the [atlassian/jira-software](https://hub.docker.com/r/atlassian/jira-software/) image will create a [H2](https://www.h2database.com/html/main.html) database for evaluation purposes, this should NOT be used in production. You can either allow this chart to create a [PostgreSQL](https://hub.docker.com/_/postgres) instance specifically for Jira Software by specifying ```postgresql.enabled``` as ```true``` or you can use an external PostgreSQL instance by specifying the connection details on ```psql```. 
