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
10. Create an RDS database with ```Postgressql Engine```. log in to the database and run commands in the ```public_schema.md``` script in the default **postgres database** to **grant permission to the public schema** such that it can communicate with the ```jira-software pod```.

11. In your client node, create and update the ```jira-values.yml``` file as per your requirement so it connects to your database.

***NB:*** By default the [atlassian/jira-software](https://hub.docker.com/r/atlassian/jira-software/) image will create a [H2](https://www.h2database.com/html/main.html) database for evaluation purposes, this should NOT be used in production. You can either allow this chart to create a [PostgreSQL](https://hub.docker.com/_/postgres) instance specifically for Jira Software by specifying ```postgresql.enabled``` as ```true``` or you can use an external PostgreSQL instance by specifying the connection details on ```psql```. 

- In the ```service``` section of the ```jira-values.yml``` file, you can specify a ```LoadBalancer``` service so you can reach the cluster from the internet.

![Screenshot 2024-12-13 115209](https://github.com/user-attachments/assets/cad8e8b7-83df-43f0-b1ad-19be75f12d0b)

12. Install the Helm Chart and pass the values by using the following command:
```sh
helm upgrade --install --namespace default --values ./jira-values.yml my-release stevehipwell/jira-software
```
![image](https://github.com/user-attachments/assets/d03d13af-7e21-4ccc-a716-023ab732d8da)

13. Check if the ```jira-software pod``` is running:
```sh
kubectl get pods
```
![Screenshot 2024-11-18 191457](https://github.com/user-attachments/assets/dc344034-2f87-4c6a-b3c1-24cd8d069605)

14. Get the ```LoadBalancer``` DNS along side the port on which it runs and paste on a browser. E.g. ```LoadBalancerDNS:8080```
```sh
kubectl get svc
```
![Screenshot 2024-11-18 191452](https://github.com/user-attachments/assets/e70553dc-9b20-4bbc-b701-1b66404bdb48)

15. In the GUI that you see, you can choose any you wish but for this demo, we will select the second option since we have an external database we wish to communicate with manually.
![Screenshot 2024-11-18 170647-1](https://github.com/user-attachments/assets/88d3b9f3-e240-452a-8a74-d5c5bdf09237)

- After you select the second option, pass in the ```database credentials``` as asked by the interface. Then click ```Next``` directly or "Test Connection``` first before clicking on **Next**.
![Screenshot 2024-11-18 170733-2](https://github.com/user-attachments/assets/06d86882-7618-4c1f-86a1-6bd4c98f62e6)

16. After hitting Next, it might not work. It might give you the following ```error``` message.
![Screenshot 2024-11-18 191704](https://github.com/user-attachments/assets/695748cd-35c8-4386-b3f1-4da61818d220)

- To solve this, ```rerun step 12```. After which, you can grab the ```LoadBalancerDNS:8080``` once again and run on the browser and this time, you will get the below outcome which indicates that your connection with the database has been established. Frome here, you can click **Next** pass your **License**.
![Screenshot 2024-11-18 173856-3](https://github.com/user-attachments/assets/933f5e9c-9cf3-4ee2-a032-ee02cf139d2d)

17. You can check all the tables present in your **database schema**, e.g. **Public** and you will see all the tables that were created as a result of the connection established between your **database** and the **Jira-software pod**.

![Screenshot 2024-11-18 185730](https://github.com/user-attachments/assets/11e48093-c45c-4870-af1e-5276e7d43445)

***NB:*** Another very interesting way you could elaborate on this project is to do the following:
- In case you had a database pod running in the cluster already, you could connect it to the jira-software pod using the values.yml file.

- Once the ```jira-software pod``` is communicating with the ```database pod``` in the cluster, you could create an RDS database, and create a database in it with a similar name as the one you created in the database pod.
    - Create a **Publication** for all the tables in the **database pod**, using the ```dump``` command, you can automatically create all the tables available in your **database pod** in your **RDS Postgres DB**.
    - After this, while in the **RDS Postgres DB**, you could create a **subscription** to the **Publication** in the database pod such that every data that goes into the database pod will be automatically replicated in the **RDS Database Pod**. 


