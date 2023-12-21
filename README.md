# mirantis-festive-kubernetes-challenge
Mirantis Labs Christmas Special: Festive Kubernetes Challenge 2023

Information about the challenge can be found here: <https://www.mirantis.com/labs/learning/techtalks/mirantis-labs-christmas-special-code-challenge>

## Requirements

It is expected to have **kubectl** and **helm** installed locally.

## Steps to prepare the environment

The application (or the chart) can be deployed on any k8s cluster.

These instructions are for creating a **k0s** cluster. More about it can be found here: <https://k0sproject.io/>

### (optional) Adjust the Vagrantfile

You can adjust it to match your needs and requirements or at least explore it with:

```bash
cat Vagrantfile
```

### Deploy the infrastructure

It is enough to execute:

```bash
vagrant up
```

*Note that before you can deploy a k0s cluster on those, you must execute a few additional steps on each machine that is part of the environment. Under normal conditions, these are not needed, but the box used in this example has an issue with the machine ID.*

```bash
vagrant ssh <machine-id>

echo -n | sudo tee /etc/machine-id

sudo rm /var/lib/dbus/machine-id

sudo ln -s /etc/machine-id /var/lib/dbus/machine-id

sudo reboot
```

### Install the k0sctl tool

One possible way to accomplish this, if you are working on a Linux machine, is to execute the following commands:

```bash
wget https://github.com/k0sproject/k0sctl/releases/download/v0.16.0/k0sctl-linux-x64 -O k0sctl

sudo mv k0sctl /usr/bin/

sudo chmod +x /usr/bin/k0sctl
```

More about the **k0sctl** tool can be found here: <https://github.com/k0sproject/k0sctl>

### (optional) Configure k0sctl command completion

This can be done with:

```bash
k0sctl completion | sudo tee /etc/bash_completion.d/k0sctl
```

### (optional) Adjust the k0s cluster definition

Or at least explore the file:

```bash
cat k0s-cluster.yaml
```

### Deploy the k0s cluster

It is as simple as executing:

```bash
k0sctl apply --config k0s-cluster.yaml
```

### Get cluster configuration

You will need a way to communicate with the cluster. It can be done with:

```bash
k0sctl kubeconfig --config k0s-cluster.yaml > ~/.kube/config
```

*Keep in mind that this will replace your current **~/.kube/config** file.*

## Deploy the application

The application can be configured via the following parameters:

 - **region** - a string defining the target region. The default value is ***northpole***

 - **replicaCount** - an integer defining the number of replicas. The default value is ***1***

 - **nodePort** - an integer defining the NodePort to publish the service to. The default value is ***32023**

In order to deploy it with the default values as release named ***test1***, you must execute:

```bash
helm install test1 toy-manufacturing
```

If you want to deploy it in another region, for example ***southpole***, you can execute:

```bash
helm install test2 toy-manufacturing --set region="southpole"
```

If you want to change both the region and increase the capacity, you can execute:

```bash
helm install test3 toy-manufacturing --set region="southpole" --set replicaCount=3
```

*Note that the above commands will deploy the application in the **default** namespace. Should you want to change it, add the following **--namespace <target-ns>** to the command.*

After the application is deployed, you can see it with:

```bash
helm list
```

You can check a working application (release) by:

```bash
curl http://<node-ip>:<node-port>/

curl http://<node-ip>:<node-port>/api/data

curl http://<node-ip>:<node-port>/api/region
```

Once you are done, you can delete it with:

```bash
helm uninstall <release-name>
```
