# k8-example
# Kubernetes controllers
- Example on StatefulSet
- Example on DaemonSets
- Example on Deployment

Kubernetes manages all workloads as Pods. But you should almost never create and manage Pods yourself.

Instead you create Deployments, StatefulSets or DaemonSets where a controller takes care of that.

- StatefulSets controller are for stateful applications, where the identity of a Pod matters. The StatefulSet controller creates a number of Pods in order and actually numbers them. If it replaces them because the configuration changes, it keeps the names the same. A StatefulSet can also contain a volumeClaimTemplate; the controller will create PVCs for each Pod. The relation of Pod and PVC stays the same always, so they keep the same data, even when they're replaced. This makes them viable for applications where you have to treat your instances (or at least their identity) with more care.

- DaemonSets manage groups of replicated Pods. However, DaemonSets attempt to adhere to a one-Pod-per-node model, either across the entire cluster or a subset of nodes. A Daemonset will not run more than one replica per node. Another advantage of using a Daemonset is that, if you add a node to the cluster, then the Daemonset will automatically spawn a pod on that node, which a deployment will not do.

- Deployments controller creates ReplicaSets which means a bunch of the same pods, same everything, just scheduled individually. If the deployment changes the Deployment controller creates a new ReplicaSet to replace the old one and takes care of a rolling deployment, meaning scaling one down and the other up as long as the PodDisruptionBudget is not violated. A Deployment works great for stateless applications where you can treat the pods as cattle.


# An introduction to Kubernetes storage

Before diving into the specifics of Kubernetes storage classes, you must understand the basics of persistent volumes and persistent volume claims.

A persistent volume (PV) is a Kubernetes object that represents a piece of storage, either locally or on the cloud. Pods use the PV of a cluster to store their data. A persistent volume claim (PVC) is a Kubernetes object representing a claim on a PV by forming a one-to-one mapping with your persistent volume and specifying what’s required for the persistent volume to be used by your pod.
PersistentVolume (PV) is a Kubernetes resource that represents a piece of storage in a cluster. It's a cluster-wide resource that can be used by multiple pods in a Kubernetes cluster. It can be created in a number of ways, including statically provisioning a volume, dynamically provisioning a volume, or importing an existing volume.
A PV is a way to decouple the storage from the pod, allowing the pod to access the storage independently of the pod's lifecycle. PVs are designed to be used by multiple pods, which means that they can have a lifecycle independent of any pod that uses them.

Whenever you need storage for your stateful application, you should provision a disk for your PV. For example, gcePersistentDisk on Google Cloud:
bash

The next step is creating a PVC that claims the storage. The same PVC will be used in your pod definition, which will allow your pods to use the disk. The above procedure, in which you need to provision a disk for your PV, is referred to as static storage provisioning, because you create the disks manually.
PersistentVolumeClaim (PVC) is a Kubernetes resource that represents a request for storage by a pod. It can specify requirements for storage, such as the size, access mode, and storage class. Kubernetes uses the PVC to find an available PV that satisfies the PVC's requirements.
A PVC is created by a pod to request storage from a PV. Once a PVC is created, it can be mounted as a volume in a pod. The pod can then use the mounted volume to store and retrieve data.

However, it’s not convenient or practical to manually create a disk and PV every time you need storage. Kubernetes solves this problem by providing a way to make your storage provisioning more dynamic with dynamic storage provisioning, where PVs and disks are created on the fly when PVCs or pods request storage.
What is dynamic storage provisioning?

Dynamic storage provisioning is the process of automatically allocating storage to containers in a Kubernetes cluster. This is done automatically by the Kubernetes control plane when it detects that a container needs storage.

The control plane will select a storage class based on the container’s requirements, then automatically create a new persistent volume using that storage class. After that, as in static provisioning, the PV is attached to the container, and the container can use the storage via a PVC.
What is a storage class?

StorageClass objects were introduced in Kubernetes 1.5 and have since become essential to Kubernetes storage.

A storage class in Kubernetes defines different storage types, which allows the user to request a specific type of storage for their workloads. Storage classes also allow the cluster administrator to control which type of storage is used for specific workloads by specifying a type of storage.

The StorageClass object contains information about the provider, such as Amazon EBS or Google Cloud Storage, as well as which capabilities, such as replication factor or encryption, the storage should have. Kubernetes will then use information from the storage class when it creates new persistent volumes.

Why use Kubernetes storage classes?

Kubernetes storage classes enable an administrator to create and manage multiple storage configurations and bind them to individual applications or workloads. This provides greater flexibility and control over managing storage resources in a Kubernetes cluster, as you don’t need to configure and create different types of storage with various specifications every time a storage request is made.

The dynamic provisioning and class isolation of storage can be used in many different scenarios, including the following common use cases.
