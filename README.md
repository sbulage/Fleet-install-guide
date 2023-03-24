![fleet_log](https://user-images.githubusercontent.com/12134754/227513601-2b19be45-098f-4307-bfdd-8137d272f02f.png)
---
# What is Fleet?

- **Cluster engine:** Fleet is a container management and deployment engine designed to offer users more control on the local cluster and constant monitoring through GitOps. Fleet focuses not only on the ability to scale, but it also gives users a high degree of control and visibility to monitor exactly what is installed on the cluster.

- **Deployment management:** Fleet can manage deployments from git of raw Kubernetes YAML, Helm charts, Kustomize, or any combination of the three. Regardless of the source, all resources are dynamically turned into Helm charts, and Helm is used as the engine to deploy all resources in the cluster. As a result, users have a high degree of control, consistency, and auditability.

For information read it here: [Fleet Documentation](https://fleet.rancher.io/)

---

# Install Fleet

## Pre-requisites:

- Check helm is installed in the system.

        helm version

- Install `helm` (if not installed)

        sudo zypper install helm

- Download [`kubectl`](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)



- In this guide we're using K3s Kubernetes cluster.
For this we will use the dockerize way to install [K3s](https://docs.k3s.io/) Kuberenets is install [K3d](https://k3d.io/v5.4.9/#installation).

To install ***k3d*** follow this [guide](<link>).

---

### Virtual Machines configuration used in this guide.

- 1 VM with 4 CPU and 8GB Ram

---

 ### In this guide you will get introduce to fleet and GitRepo CRD.

 Fleet is fundamentally a set of Kubernetes custom resource definitions (CRDs) and controllers that manage GitOps for a single Kubernetes cluster or a large scale deployment of Kubernetes clusters. It is a distributed initialization system that makes it easy to customize applications and manage HA clusters from a single point. [Click here for more info.](https://fleet.rancher.io/)

---

## Let's begin with Installation and Setup.

Initially we will install the Docker in the system.(Skip this step if Docker is already present in the system)

### Docker Installation

- Install Docker and Check it's running in the system.

        sudo zypper install docker

- Start the docker daemon during boot:

        sudo systemctl enable docker

- Join the docker group that is allowed to use the docker daemon:

        sudo usermod -G docker -a $USER

- Restart the docker daemon:

        sudo systemctl restart docker

- Verify docker is running:

        docker version

### Fleet Installation

- Install `fleet-crd` via `Helm`.

        helm -n cattle-fleet-system install --create-namespace --wait \
        fleet-crd https://github.com/rancher/fleet/releases/download/v0.6.0-rc.4/fleet-crd-0.6.0-rc.4.tgz

    wait for few minutes to fetch information and create respective CR's in the system.

- Install `fleet` via `Helm`.

        helm -n cattle-fleet-system install --create-namespace --wait \
        fleet https://github.com/rancher/fleet/releases/download/v0.6.0-rc.4/fleet-0.6.0-rc.4.tgz

- Check fleet status

        kubectl -n fleet-local get fleet

- Add a Git Repo to Watch

        cat > example.yaml << "EOF"
        apiVersion: fleet.cattle.io/v1alpha1
        kind: GitRepo
        metadata:
          name: sample
          # This namespace is special and auto-wired to deploy to the local cluster
          namespace: fleet-local
        spec:
          # Everything from this repo will be ran in this cluster. You trust me right?
          repo: "https://github.com/rancher/fleet-examples"
          paths:
          - simple
        EOF

- Create above `GitRepo` CRD

        kubectl apply -f example.yaml

- Check the deployment is created there or not from the git repository added.

        kubectl get deploy frontend

Now try to play with the fleet by modifying the application and see whether it get reverted or not.

Want to know more about fleet multicluster management stay connected...
### Play with Fleet...
