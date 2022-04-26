# Kubernetes Guide

Official Documentation: [Kubernetes Documentation](https://kubernetes.io/docs/home/)

Reset a machine which is a part of a kubernetes cluster:

```
$ kubeadm reset
```

To completely uninstall kubernetes:

```
$ apt purge kubectl kubelet kubeadm kubernetes-cni kube*
$ apt autoremove
```

Make sure docker is installed before installing kubernetes:
[Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

Choose which version of kubernetes you want to install:

```
$ curl -s https://packages.cloud.google.com/apt/dists/kubernetes-xenial/main/binary-amd64/Packages | grep Version | awk '{print $2}'
```

Install the version you want:

```
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
$ echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
$ sudo apt update
$ sudo apt install kubelet=<version> kubectl=<version> kubeadm=<version>
```

Be sure to disable swap before creating a cluster node (on each computer):
[How to Permanently Disable Swap in Linux](https://www.tecmint.com/disable-swap-partition-in-centos-ubuntu/)

Create the head node (may need sudo):

```
$ kubeadm init --pod-network-cidr=192.168.0.0/16
```

Follow the instructions on the command window (like creating a .kube directory)

If flannel gets set up fine, good. If not, use calico (maybe use Calico anyways)
[Quickstart for Calico on Kubernetes](https://docs.projectcalico.org/getting-started/kubernetes/quickstart)

On worker nodes, install kubernetes as mentioned above.
Then run the following command on the head node:

```
$ kubeadm token create --print-join-command
```

Copy the command and run it on the worker node and it should join the cluster (may take a few seconds). 

Test the cluster by creating an example deployment:

```
$ kubectl apply -f https://k8s.io/examples/controllers/nginx-deployment.yaml
```

Some common kubernetes commands:

```
$ kubectl get <resource_type> [--all-namespaces]
```

```
$ kubectl describe <resource_type> <resource_name> [--namespace if_any]
```

Some resource types: node, pod, deployment, namespace, secret

A namespace can be easily created using a yaml file. Example file can be found [here](https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/).
To use the namespace, you can create a context, attach the namespace to the context and then make the created context the current context.
[Here](https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/#create-pods-in-each-namespace) is how you can do that.
Then you can move around between namespaces by simply changing the context everytime.

You can also attach the namespace to the current context:

```
$ kubectl config set-context --current --namespace=<new_namespace>
```

To enable kubernetes to pull images from stogit, we need to create a secret:
[Pull an Image from a Private Registry](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)
Remember that the docker repositories on stogit can be reached using:

```
docker.cs.stolaf.edu:443
```

IMPORTANT: The secret is only for the current namespace.
If you change namespaces, you need to either create a new secret, or copy the secret from the current namespace to the new namespace
[Here](https://www.revsys.com/tidbits/copying-kubernetes-secrets-between-namespaces/#:~:text=A%20simple%20way%20of%20copying,%2D%20%2C%20and%20you%20are%20done!) is how to.