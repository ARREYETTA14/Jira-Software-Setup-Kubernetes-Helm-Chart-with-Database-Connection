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

5. Install the Kubectl version compatible with Cluster Version 1.31 by running all the required commands from the [AWS Document to install Kubectl!](kubectl aws documentation)
