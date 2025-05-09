# tuxcon 2025 - Chaos Challenge: Kubernetes Containerization Live with You!

## 0. Pre-requisites

For the host machine we will need:

- [Vagrant](https://www.vagrantup.com) - A development environment automation tool.
- [VNC Viewer Windows](https://www.tightvnc.com) - To connect to the VM GUI
- [VirtualBox](https://www.virtualbox.org) - A free and open-source hosted hypervisor for x86 virtualization.
- [VMWare Fusion Mac ( payed )](https://www.vmware.com/products/fusion.html) - A paid hosted hypervisor for x86 virtualization.

## 0.1 Offline setup

For doing the workshop exercises offline we'll need a Virtual Machine based on `Ubuntu 24.04`. We can create one, using
any of the freely available desktop hypervisors:

Free and Open Source:

- [VirtualBox](https://www.virtualbox.org)

Free for personal use:
> Note: Both VMware Workstation Pro and Fusion Pro are now free for personal use. More details you can find [here](https://blogs.vmware.com/workstation/2024/05/vmware-workstation-pro-now-available-free-for-personal-use.html)

- [VMware Workstation Pro](https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware+Workstation+Pro)
- [VMware Fusion Pro](https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware+Fusion)

Build-in Virtualization:

- Hyper-V in Windows 11 Pro or Enterprise
- KVM/XEN in Linux Desktops

To save some time (and effort), we can use [Vagrant](https://www.vagrantup.com) to automate the creation of the VM.
Vagrant by HashiCorp is an easy to use tool that allow us to quickly create a Virtual Machine based development
environments using automation.

> Note: We are not going to spend much time explaining Vagrant in details here, but if you have any questions/problems
> please ask.

If you want to learn more about Vagrant a good starting point is their [Get Started](https://developer.hashicorp.com/vagrant/tutorials/getting-started)
tutorial.

## 0.2 Lab VM setup using Vagrant

- Install [Vagrant](https://developer.hashicorp.com/vagrant/docs/installation)
- Make sure your desktop Virtualization software is configured
- Create a `Vagrantfile`
- Run `vagrant up` and wait for the VM to be up and running.

Lets start by creating the Vagrantfile:

```shell
~$ cat Vagrantfile
```

Here is simple example of `Vagrantfile` ( different from ours ):

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-24.04"
  config.vm.box_version = "202404.26.0"
end
```

More details about the Vagrant Box that we'll be using you can find  [here](https://app.vagrantup.com/bento/boxes/ubuntu-24.04)
Next, we are going to create the lab VM using `vagrant up`:

> Note: The output on your machine might differ based on the platform and hypervisor you are using

```bash
~$ vagrant up
```

```shell
Bringing machine 'default' up with 'vmware_desktop' provider...
==> default: Box 'bento/ubuntu-24.04' could not be found. Attempting to find and install...
    default: Box Provider: vmware_desktop, vmware_fusion, vmware_workstation
    default: Box Version: 202404.26.0
==> default: Loading metadata for box 'bento/ubuntu-24.04'
    default: URL: https://vagrantcloud.com/api/v2/vagrant/bento/ubuntu-24.04
==> default: Adding box 'bento/ubuntu-24.04' (v202404.26.0) for provider: vmware_desktop (arm64)
    default: Downloading: https://vagrantcloud.com/bento/boxes/ubuntu-24.04/versions/202404.26.0/providers/vmware_desktop/arm64/vagrant.box
==> default: Successfully added box 'bento/ubuntu-24.04' (v202404.26.0) for 'vmware_desktop (arm64)'!
==> default: Cloning VMware VM: 'bento/ubuntu-24.04'. This can take some time...
==> default: Checking if box 'bento/ubuntu-24.04' version '202404.26.0' is up to date...
==> default: Verifying vmnet devices are healthy...
==> default: Preparing network adapters...
==> default: Starting the VMware VM...
==> default: Waiting for the VM to receive an address...
==> default: Forwarding ports...
    default: -- 22 => 2222
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Configuring network adapters within the VM...
==> default: Waiting for HGFS to become available...
==> default: Enabling and configuring shared folders...

~$
```

```shell
~$ vagrant box list
bento/ubuntu-24.04 (vmware_desktop, 202404.26.0, (arm64))

~$ vagrant status default
Current machine states:

default                   running (vmware_desktop)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down, or you can run `vagrant suspend` to simply suspend
the virtual machine. In either case, to restart it again, run
~$
```

```shell
~$ vmrun list

Total running VMs: 1
Total running VMs: 1
~/dojolabs/.vagrant/machines/default/vmware_desktop/172785f5-4122-4d7e-968d-29e49243385b/ubuntu-24.04-aarch64.vmx
~$
```

Now we have our Linux machine lets ssh inside it. With `vagrat` is very simple just use the command below:

```bash
~/demo$ vagrant ssh

Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.8.0-31-generic aarch64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu Jul  4 11:04:26 AM UTC 2024

  System load:  0.0                Processes:             252
  Usage of /:   10.7% of 29.82GB   Users logged in:       0
  Memory usage: 3%                 IPv4 address for eth0: 192.168.59.170
  Swap usage:   0%
```

### 0.2 Resize VM

In any case we can resize the VM if we need more space. Lets do that:

```bash
vagrant@ubuntu:~$ sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
vagrant@ubuntu:~$ sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

!!! For simplicity i will remove `vagrant@ubuntu`


## 0.2.1 Clone the workshop GIT repository

```bash
~$ git clone https://github.com/DojoBits/tuxcon2024.git
~$ cd tuxcon2024/demo
```

### 1. Install Docker

Lets start by installing Docker. We will use Docker to run our containers. Here
is a script to install it but before executing it take a look.

`cat install-docker.sh`

```bash
~/tuxcon2024/demo$ bash install-docker.sh
```

## 2.0 Overview Django app

Lets overview our Django app which is in `myproject` folder

We have simple Django app with `clock`, `random generated number` and a
JavaScript which will call a `service_status` page every second. We will use it
to simulate real service.


Lets first install pip, venv and bash-completion:

```bash
~/tuxcon2024/demo$ sudo apt install python3-pip python3.12-venv bash-completion -y

```

We will create a virtual environment for our Django app to isolate it from the system Python packages. Lets create the virtual environment and activate it:

```bash
~/tuxcon2024/demo$ python3 -m venv myenv
# Activate the virtual environment
~/tuxcon2024/demo$ source myenv/bin/activate
(myenv) ~/tuxcon2024/demo$
```

Now lets install the requirements for our Django app overview the app

```bash
(myevn) ~/tuxcon2024/demo$ pip3 install -r requirements.txt
(myevn) ~/tuxcon2024/demo$ cd myproject/
(myevn) ~/tuxcon2024/demo/myproject$ ls
total 140
-rw-rw-r-- 1 vagrant vagrant 131072 Oct 30 19:13 db.sqlite3
-rwxrwxr-x 1 vagrant vagrant    665 Oct 30 19:13 manage.py
drwxrwxr-x 2 vagrant vagrant   4096 Oct 30 19:13 myproject
drwxrwxr-x 3 vagrant vagrant   4096 Oct 30 19:13 service
```

We can get our Virtual Machine IP address with the command below. To see on which interface to access the service. In our case it is `eth0`. For this VM the IP is 192.168.59.180 but it may differ on your machine.

```bash
(myenv) ~/tuxcon2024/demo/myproject$ ip a s
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:4f:6c:e6 brd ff:ff:ff:ff:ff:ff
    altname enp2s0
    altname ens160
    inet 192.168.59.180/24 metric 100 brd 192.168.59.255 scope global dynamic eth0
       valid_lft 1614sec preferred_lft 1614sec
    inet6 fe80::20c:29ff:fe4f:6ce6/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:a2:53:a6:3a brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
```

If you want to go hacky you can save your IP in a variable:

```bash
(myenv) ~/myproject$ export MY_IP=$(ip a s eth0 | grep -w inet | awk '{print $2}' | cut -d/ -f1)
(myenv) ~/myproject$ echo $MY_IP
192.168.59.180
```

After the overview lets start the app to see if its working from our computer


```bash
(myenv) ~/myproject$ python3 manage.py runserver 0.0.0.0:8000
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
November 02, 2024 - 07:16:48
Django version 4.2.7, using settings 'myproject.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.

```

Now from our browser we can access the service by navigating to `http://192.168.59.180:8000`


### 3.0 Build Docker image

Now lets build our Docker image. In our repo we have a `Dockerfile` which will
build our image. Lets overview it first. The Docker files are in `demo` folder.

```bash
~/myproject$ cd ~/tuxcon2024/demo/
```
```

```Dockerfile
:~/myproject$ vim Dockerfile
```

Build the image:

```bash
~/myproject$ docker build -t django-service:1.0.0 .
```

Lets overview the image:

```bash
~/myproject$ docker images
REPOSITORY       TAG       IMAGE ID       CREATED         SIZE
django-service   1.0.0     4ea0350aabc4   5 seconds ago   176MB
```


### 3.1 Start Docker container

Now lets start our Docker container:

```bash
~/myproject$ docker run --name django -d -p 8000:8000 django-service:1.0.0
72317c20e7762b73d057017f317a95b291f9447c3a72308fd5209de451cedcb6
```

Lets overview the container:

```bash
~/myproject$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS         PORTS                                       NAMES
ee991b86b863   django-service:1.0.0   "/bin/sh -c 'python …"   4 seconds ago   Up 3 seconds   0.0.0.0:8000->8000/tcp, :::8000->8000/tcp   django
```

Now lets again open Virtual Machine GUI and open the browser to
`http://192.168.59.180:8000` and see if our application is working from the
container.

### 3.2 Push Docker image to local Docker registry

Because we are not sure if you have your own Docker hub account or any other
repository we decided to show you how you can push the image to a local Docker
registry. Lets create a local Docker registry to push our image there. This is
like Docker hub but locally on our machine. It's great for testing and
development.

```bash
~$ docker run -d -p 5000:5000 --name registry registry:2
Unable to find image 'registry:2' locally
Pulling from library/registry
720f3032cd11: Pull complete
d259642272e5: Pull complete
6e496f333012: Pull complete
e961822193ae: Pull complete
b98a2db964ee: Pull complete
Digest: sha256:ac0192b549007e22998eb74e8d8488dcfe70f1489520c3b144a6047ac5efbe90
Status: Downloaded newer image for registry:2
6b2bc5137802bfeb204e8405d3b9ec15ec9da314eae7777b47ce9b3c4feec07a
```

Now we have a local Docker registry running on port 5000. Lets tag our image and
push it to the registry.

```bash
~/myproject$ docker tag django-service:1.0.0 localhost:5000/django-service:1.0.0
```

Now lets push the image to the local Docker registry:

Check the image:

```bash
:~/demo$ docker images
REPOSITORY                      TAG       IMAGE ID       CREATED          SIZE
django-service                  1.0.0     c42b701e1993   29 minutes ago   172MB
localhost:5000/django-service   1.0.0     c42b701e1993   29 minutes ago   172MB
registry                        2         c9cf76bb104e   13 months ago    25MB
```

As you can see now we have the same image but with different tags. One is the
one we build and the other is the one we tagged with our Docker Hub username.

Now lets push the image:

```bash
~/myproject$ docker push localhost:5000/django-service:1.0.0
The push refers to repository [localhost:5000/django-service]
ff3f03750823: Pushed
23b6360dfd73: Pushed
bec590085a48: Pushed
c303e8ea9c17: Pushed
5040749fd883: Pushed
7daa74ccc116: Pushed
9b36355c1b1f: Pushed
7ebda8e9701b: Pushed
0e2caf38def5: Pushed
1.0.0: digest: sha256:c5db2ff5ca3f6ccee80ac53fca7ec0af325f201fe555e821f126f7ccb88e2ef0 size: 2204
```

Our image is now pushed to the local Docker registry. If you are familiar we have the OCI standards which provide specifications for container images and runtime. The image is stored in the registry as a tarball. The registry is like a key-value store where the key is the image name and the value is the tarball. With a simple `curl` command we can tacle the registry and see the images and tags.

```bash
~/myproject$ curl http://localhost:5000/v2/_catalog
{"repositories":["django-service"]}
```

Lets overview the tags of the image:

```bash
~/myproject$ curl http://localhost:5000/v2/django-service/tags/list
{"name":"django-service","tags":["1.0.0"]}
```

It looks fine. Now we have our image in the local Docker registry.

## Scale with Kubernetes

### 4.0 Install Kubernetes with KinD

`KinD` is a tool for running local Kubernetes clusters using Docker container. It's one of the simplest and easiest ways to run Kubernetes locally. We use it for our testing and development. Lets install it.

Always check the scripts `cat install-kind.sh` before running them.

```bash
~$ bash install-kind.sh
```

Now we have `kind` lets create the Kubernetes cluster. From [KinD
documentation](https://kind.sigs.k8s.io/docs/user/configuration/) you can get a
sample configuration file. We already have one in this repo which will create 1
control plane node and 3 worker nodes.

```bash
kind create cluster --config kind-config.yaml --name=tuxcon
Creaitng cluster "tuxcon" ...
 ✓ Ensuring node image (kindest/node:v1.32.2) 🖼
 ✓ Preparing nodes 📦 📦 📦 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
 ✓ Joining worker nodes 🚜
Set kubectl context to "kind-tuxcon"
You can now use your cluster with:

```



### 4.1 Install kubectl

`kubectl` is a command line tool for interacting with Kubernetes clusters. Do not run scripts from the internet without checking them first. It's a good practice `cat`, `vim` or `less` the script before running it. You can do that with the command below:

```bash
~$ vim install-kubectl.sh
```
Now lets install our `kubectl` which will be used to interact with our Kubernetes cluster.

```bash
~$ bash install-kubectl.sh
~/demo$ bash install-kubectl.sh
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   138  100   138    0     0     17      0  0:00:08  0:00:07  0:00:01    28
100 52.4M  100 52.4M    0     0  2644k      0  0:00:20  0:00:20 --:--:-- 11.0M
^[^[Client Version: v1.32.3
Kustomize Version: v5.5.0
Server Version: v1.32.2

```

Enable auto completion. Same as before lets overview the script before running it:

```bash
~$ cat k8s-autocompletion.sh
#!/bin/bash
echo "source <(kubectl completion bash)" >>~/.bashrc # add autocomplete permaneently to your bash shell.
echo 'alias k=kubectl' >>~/.bashrc
echo 'complete -o default -F __start_kubectl k' >>~/.bashrc
source ~/.bashrc
```

```bash
~$ bash k8s-autocompletion.sh
~$ source ~/.bashrc
```

Lets overview the cluster:

```bash
~$ kubectl get nodes
NAME                     STATUS   ROLES           AGE   VERSION
tuxcon-control-plane   Ready    control-plane   24m   v1.32.2
tuxcon-worker          Ready    <none>          23m   v1.32.2
tuxcon-worker2         Ready    <none>          23m   v1.32.2
tuxcon-worker3         Ready    <none>          23m   v1.32.2
```

### 5.0 Deploy Django service

Lets create a deployment for our Django service which we created earlier in a container and pushed to our local Docker registry.:

Kubernetes localhost is different that's why we need to use the IP address of the
Docker host. We can get it with the command below:

```bash
~$ docker network inspect -f '{{.IPAM.Config}}' kind
[{fc00:f853:ccd:e793::/64   map[]} {172.18.0.0/16  172.18.0.1 map[]}]
```

Now lets create a deployment for our Django service:

! You may notice we use different image here. Because our docker registry and Kubernetes are isolated we already created a new image with the tag `dojobits/django-service:1.0.0` and pushed it to the Docker registry. In that case you will use the public image not the one you created but they are the same.

!!! Keep in mind because this was create in event lab and everyone is using the same IP we may hit the docker hub free limit. This is not from a repo point of view is from your Docker account. If you hit that issue call the speaker to help you out.

```bash
~$ kubectl create deployment django-app --image=dojobits/django-service:1.0.0 --port=8000 --replicas=1

deployment.apps/django-app created
```

Lets overview the deployment:

```bash
:$ kubectl get deployments
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
django-app   1/1     1            1           85s
```

Lets overview the pods:

```bash
~$ kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
django-app-55d6f6f587-wjs5m   1/1     Running   0          100s
```

All good but the problem is we cannot access the service from outside the cluster.
For that we need to expose it as a service.

Lets expose our deployment as a service:

```bash
~$ kubectl expose deployment django-app --type=LoadBalancer --port=8000 --target-port=8000
service/django-app exposed
```

This will create a service with type `LoadBalancer` which will expose it to port
8000 and will forward the traffic to port 8000 of the container.

Lets check the Kubernetes services:


```bash
:~$ kubectl get svc
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
django-service   LoadBalancer   10.96.200.235   <pending>     80:32226/TCP   15s
kubernetes       ClusterIP      10.96.0.1       <none>        443/TCP        12m
```

As you can see the LoadBalancer is still pending. This is because we don't have
a LoadBalancer in our cluster. Lets do that.


### 5.1 Install and configure LoadBalancer

We will install `metallb` to provide us with a LoadBalancer for our cluster.

```bash
~$ kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml

...
configmap/metallb-excludel2 created
secret/webhook-server-cert created
service/webhook-service created
deployment.apps/controller created
daemonset.apps/speaker created
validatingwebhookconfiguration.admissionregistration.k8s.io/metallb-webhook-configuration created
```

Find Docker subnet

```bash
~$ docker network inspect -f '{{.IPAM.Config}}' kind
[{fc00:f853:ccd:e793::/64   map[]} {172.18.0.0/16  172.18.0.1 map[]}]

```

Lets overview the metallb config file where we configure IP address pool and our
L2Advertisement. We need that to make our LoadBalancer work. In our example we need to put the IP address pool in the same subnet as our Docker subnet.

```bash
~$ cat metallb-config.yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: example
  namespace: metallb-system
spec:
  addresses:
  - 172.18.255.200-172.18.255.250
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: empty
  namespace: metallb-system

```

As you can see we have a pool of addresses which are in the same subnet as our
docker subnet 172.18.0.0/16.

```bash
~$ kubectl apply -f metallb-config.yaml
ipaddresspool.metallb.io/example created
l2advertisement.metallb.io/empty created
```

Lets check the services again and see what is going on:

```bash
~$ kubectl get svc
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)          AGE
django-app   LoadBalancer   10.96.51.251   172.18.255.200   8000:32275/TCP   110s
kubernetes   ClusterIP      10.96.0.1      <none>           443/TCP          17m

```

## Validate service is running

```bash
~$ curl http://172.18.255.200:8000
<!DOCTYPE html>
<html>
<head>
    <title>Service Status Dashboard</title>
    <link href="https://fonts.googleapis.com/css?family=Roboto&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Roboto', sans-serif;
            margin: 0;
...
```
### 5.2 Access the service from GUI

In the real world you will access the MetalLB IP from anywhere but in our
Hands-on because we have multiple virtualization layers and network isolations
we can access the service only from the VM. So locally with `curl` we see it works targeting the MetalLB IP but for GUI we need a Desktop VM which is fine but its heavy. For that reason we will use `kubectl port-forward` to access the service from our host machine by forwarding the port to our VM IP.

Because we have a development env we use kind it shard to use the MetalLB IP from the VM. Lets use Kubernetes port-forward to access the service from our host machine. But we will export it to our VM public IP 192.168.59.180

```bash
~$ kubectl port-forward --address 0.0.0.0 svc/django-app 8000:8000
```

Now we can open the Kubernetes Django HTTP from our host machine at http://172.18.255.200:8000 to see our status page. Now this page is running inside Kubernetes cluster. Remember IP address may differ on your machine.

### 6.0 Scale the service

Now lets scale our service to 3 replicas. This means 3 instances of our Django will be serving our requests and because we are using LoadBalancer the traffic will be distributed between the 3 replicas.

```bash
~$ kubectl scale deployment django-app --replicas=3
```

Lets verify by checking the Deployment, ReplicaSet and Pods:

```bash
~$ kubectl get deployments
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
django-app   3/3     3            3           7m46s
```

```bash
~$ kubectl get rs
NAME                    DESIRED   CURRENT   READY   AGE
django-app-6665b76f87   3         3         3       7m54s
```

```bash
~$ kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
django-app-6665b76f87-4j7zv   1/1     Running   0          8m
django-app-6665b76f87-7z5zv   1/1     Running   0          8m
django-app-6665b76f87-8z5zv   1/1     Running   0          8m
```

Now we have 3 replicas of our Django service running in our Kubernetes cluster.

### 6.1 Clean up

Now lets clean up our cluster. We will delete the entire cluster.

```bash
~$ kind delete cluster --name=tuxcon
Deleting cluster "tuxcon" ...
```

### 7.0 Chaos Engineering

What is Chaos Engineering? Chaos Engineering is the discipline of experimenting
on a distributed system in order to build confidence in the system’s capability
to withstand turbulent conditions in production. The goal is to uncover system
weaknesses before they become a problem.

I will give you the tool that you can convince your beloved half that you are
working and not just playing games.


We will use `KubeDoom` which is a game that runs in Kubernetes. The idea is
that you are playing the game and when you kill monsters you actually kill
Pod's. This is a fun way to learn about Kubernetes and Chaos Engineering.

The main goals is to kill Pods but our services should not stop working. This
is the main goal of Chaos Engineering. To test the system and see if it can
withstand turbulent conditions.

[KubeDoom](https://github.com/storax/kubedoom)

Lets install KubeDoom by cloning the repo:

```bash
~$ git clone https://github.com/storax/kubedoom.git
~$ cd kubedoom
```

We will use `kind` to create another cluster for KubeDoom.

We need to make a small change in the Kind configuration file `kind-config.yaml`. We will remove the listening to localhost ,so that we can access the game from our host machine. Comment or remove line 11 `listenAddress: "127.0.0.1`

Now when we create the cluster we can access the game from our host machine.

Be hacky and use `sed` to change `kind-config.yaml`:

```bash
~$ sed -i '/listenAddress/d' kind-config.yaml
```

Here is another way to do it manually.

```bash
~$ vim kind-config.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  image: kindest/node:v1.23.0@sha256:49824ab1727c04e56a21a5d8372a402fcd32ea51ac96a2706a12af38934f81ac
- role: worker
  image: kindest/node:v1.23.0@sha256:49824ab1727c04e56a21a5d8372a402fcd32ea51ac96a2706a12af38934f81ac
  extraPortMappings:
  - containerPort: 5900
    hostPort: 5900
    #listenAddress: "127.0.0.1" # <<<<< COMMENT OUT THIS LINE
```

Lets create the cluster:

```bash
~$ kind create cluster --config kind-config.yaml
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.23.0) 🖼
 ✓ Preparing nodes 📦 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
 ✓ Joining worker nodes 🚜
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/

Lets install KubeDoom in our cluster:

```bash
~$ kubectl apply -k kubedoom/manifest/
```

We need to wait for the pods to be ready:

```bash
~$ k get pods -n kubedoom
NAME                        READY   STATUS              RESTARTS   AGE
kubedoom-5dc57468dc-rsmvx   0/1     ContainerCreating   0          24s
```


Not all cheats are in the README but here is source code file where you can find them:

Now we will create our `pods` a.k.a. monsters in our game but we will use `Deployment` to be able to scale them.


```bash
~$ kubectl create deployment kubedoom --image=nginx --replicas=3
```

We created 3 replicas of our `pods` a.k.a. monsters. Now we can play the game and kill them.

### 7.1 Play KubeDoom

Connect using VNC viewer to `VM_IP:5900` and use the password `idbehold`. In our case that is `192.168.59.180:5900` but it may differ on your machine.


Here is the snip of the cheats:

We can use:
- GOD to be unkillable - in the game press `idqd`
- AMMO to get all weapons and ammo in the game press `idkfa`
- Wall drought walls - in the game press `idspispopd`

```c
cheatseq_t cheat_mus = CHEAT("idmus", 2);
cheatseq_t cheat_god = CHEAT("iddqd", 0);
cheatseq_t cheat_ammo = CHEAT("idkfa", 0);
cheatseq_t cheat_ammonokey = CHEAT("idfa", 0);
cheatseq_t cheat_noclip = CHEAT("idspispopd", 0);
cheatseq_t cheat_commercial_noclip = CHEAT("idclip", 0);
```

You can find the cheats in the `src/st_stuff.c` file.
```bash
~$ vim dockerdoom/trunk/src/st_stuff.c
```

### 7.2 Clean up

Now lets clean up our cluster. We will delete the entire cluster.

```bash
~$ kind delete cluster --name=kind
```

# THANK YOU!

Website: [DojoBits](https://dojobits.io)
LinkedIn: [Velentin Hristev](https://www.linkedin.com/in/valentin-hristev/)


