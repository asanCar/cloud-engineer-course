# Chapter 14: Kubernetes Fundamentals

**Estimated Time:** 15 hours

**Objective:** Understand the core concepts, architecture, and basic objects of Kubernetes, enabling you to interact with and comprehend Kubernetes clusters.

## 14.1: Kubernetes Architecture

- **Objective:** Learn about the main components that make up a Kubernetes cluster and their roles.

- **Key Concepts:**

  - **Control Plane (Master Nodes):** The brain of the cluster, making global decisions (e.g., scheduling) and detecting/responding to cluster events.

    - **`kube-apiserver`:** The frontend for the Kubernetes control plane. It exposes the Kubernetes API, which is how users, management devices, and command-line interfaces interact with the cluster.

    - **`etcd`:** A consistent and highly-available key-value store used as Kubernetes' backing store for all cluster data (e.g., configurations, state).

    - **`kube-scheduler`:** Watches for newly created Pods that have no Node assigned, and selects a Node for them to run on based on resource requirements, policies, and affinity/anti-affinity specifications.

    - **`kube-controller-manager`:** Runs controller processes. Controllers are control loops that watch the shared state of the cluster through the `kube-apiserver` and make changes attempting to move the current state towards the desired state. Examples include Node controller, Replication controller, Endpoints controller, and Service Account & Token controllers.

    - **`cloud-controller-manager` (Optional):** Embeds cloud-specific control logic. It allows you to link your cluster into your cloud provider's API, and separates out the components that interact with that cloud platform from components that just interact with your cluster.

  - **Node (Worker Nodes):** Machines (VMs or physical servers) where your applications (containerized in Pods) run.

    - **`kubelet`:** An agent that runs on each node in the cluster. It makes sure that containers are running in a Pod as specified by the control plane.

    - **`kube-proxy`:** Running on each Node, `kube-proxy` is responsible for the actual implementation of Kubernetes Services. It watches for changes to Service and Endpoint objects and maintains network rules on the Node (e.g., using iptables, IPVS) to enable these virtual IP addresses for Services to function.

    - **Container Runtime:** The software responsible for running containers (e.g., Docker, containerd, CRI-O). Kubernetes is pluggable with different container runtimes via the Container Runtime Interface (CRI).

## 14.2: Core Kubernetes Objects: Declarative Configuration

- **Objective:** Understand the fundamental Kubernetes objects used to define and manage applications.

- **Key Concepts:**

  - **Declarative Model:** You declare the _desired state_ of your application or system in YAML or JSON manifest files, and Kubernetes controllers work to achieve and maintain that state.

  - **Pods:**

    - The smallest and simplest unit in the Kubernetes object model that you create or deploy.

    - Represents a single instance of a running process in your cluster.

    - Can contain one or more tightly coupled containers that share resources like network (IP address, port space) and storage (volumes).

    - Pods are ephemeral; they can be created and destroyed. If a Node fails, Pods on that Node are lost. Higher-level controllers (like Deployments) manage recreating Pods.

    - **Pod Lifecycle:** Pending, Running, Succeeded, Failed, Unknown.

  - **Labels and Selectors:**

    - **Labels:** Key/value pairs that are attached to objects, such as Pods. They are used to organize and to select subsets of objects.

    - **Selectors:** Used by controllers and Services to identify the set of objects (e.g., Pods) they operate on. Label selectors are the core grouping primitive.

  - **Annotations:**

    - Key/value pairs used to attach arbitrary non-identifying metadata to objects.

    - Can be used by tools and libraries to store custom information (e.g., build information, release notes, pointers to logging/monitoring systems).

  - **Namespaces:**

    - A way to divide cluster resources between multiple users or teams (via resource quotas).

    - Provide a scope for names. Resource names need to be unique within a namespace, but not across namespaces.

    - Not a way to provide strong security isolation, but useful for organization. Common namespaces: `default`, `kube-system`, `kube-public`.

## 14.3: Controllers & Workload Management

- **Objective:** Learn about controllers that manage Pods and ensure the desired state of your application.

- **Key Concepts:**

  - **ReplicaSets:**

    - Ensures that a specified number of Pod replicas are running at any given time.

    - If there are too many Pods, it terminates some. If there are too few, it starts more.

    - While generally not used directly, `ReplicaSets` are crucial for `Deployments`. Each version or revision of a `Deployment` is typically represented by a distinct `ReplicaSet`, enabling `Deployments` to manage application versions and facilitate rollbacks.

  - **Deployments:**

    - Provide declarative updates for Pods and ReplicaSets.

    - You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate.

    - Features: Rolling updates (zero-downtime updates), rollbacks to previous versions (by managing different ReplicaSets), scaling applications up or down.

  - **DaemonSets:**

    - Ensures that all (or some specified) Nodes run a copy of a Pod.

    - Useful for cluster-level daemons like log collectors (e.g., Fluentd), node monitoring agents (e.g., Prometheus Node Exporter), or network plugins.

  - **StatefulSets:**

    - Used to manage stateful applications (e.g., databases like MySQL, Kafka).

    - Pods get stable, unique network identifiers (e.g., `my-app-0`, `my-app-1`).

    - Pods get stable, persistent storage linked to their identity.

    - Ensures predictable and orderly lifecycle operations: Pods are deployed and scaled in a fixed order, and rolling updates are performed sequentially and automatically, one Pod at a time (or in controlled batches), to maintain application stability.

  - **Jobs & CronJobs:**

    - **Jobs:** Create one or more Pods and ensure that a specified number of them successfully terminate. Useful for batch processing or one-off tasks.

    - **CronJobs:** Create Jobs on a time-based schedule (like cron in Linux).

## 14.4: Networking in Kubernetes

- **Objective:** Understand how networking is handled within a Kubernetes cluster, enabling communication between Pods and external access.

- **Key Concepts:**

  - **Services:**

    - An abstract way to expose an application running on a set of Pods as a network service.

    - Provides a stable IP address and DNS name for a set of Pods (whose IPs can change).

    - Uses label selectors to find its target Pods.

    - **Types of Services:**

      - `ClusterIP`: Exposes the Service on a cluster-internal IP. Only reachable from within the cluster. (Default type)

      - `NodePort`: Exposes the Service on each Node’s IP at a static port.

      - `LoadBalancer`: Exposes the Service externally using a cloud provider's load balancer.

      - `ExternalName`: Maps the Service to the contents of the `externalName` field (e.g., `foo.bar.example.com`), by returning a `CNAME` record with its value. No proxying of any kind is set up.

  - **Ingress:**

    - An API object that manages external access to the services in a cluster, typically HTTP and HTTPS.

    - Can provide load balancing, SSL termination, and name-based virtual hosting.

    - Requires an **Ingress Controller** (e.g., NGINX Ingress Controller, Traefik, AWS Load Balancer Controller) to be running in the cluster to fulfill the Ingress rules.

  - **Gateway API:**

    - An official Kubernetes project representing the next generation of APIs for service networking (Ingress, Load Balancing, etc.).

    - Aims to be more expressive, role-oriented (separating concerns of cluster operator, infrastructure provider, and application developer), and extensible than Ingress.

    - **Key Resources:** `GatewayClass`, `Gateway`, `HTTPRoute`, `TCPRoute`, `TLSRoute`, `GRPCRoute`, etc.

      - **`GatewayClass`**: Defines a set of Gateways that share a common configuration and behavior. It's typically managed by the infrastructure provider.

        ```yaml
        # gatewayclass-example.yaml
        apiVersion: gateway.networking.k8s.io/v1
        kind: GatewayClass
        metadata:
          name: my-internet-gatewayclass
        spec:
          controllerName: example.com/gateway-controller # Identifies the controller that manages this class
        ```

      - **`Gateway`**: Requests a point where traffic can be translated to Services within the cluster. It's typically managed by a cluster operator and references a `GatewayClass`.

        ```yaml
        # gateway-example.yaml
        apiVersion: gateway.networking.k8s.io/v1
        kind: Gateway
        metadata:
          name: my-internet-gateway
          namespace: networking-infra # Gateways are often in a dedicated infrastructure namespace
        spec:
          gatewayClassName: my-internet-gatewayclass
          listeners:
          - name: http
            protocol: HTTP
            port: 80
            allowedRoutes:
              namespaces:
                from: Selector # Allows routes from namespaces matching a selector
                selector:
                  matchLabels:
                    expose-via-internet-gateway: "true"
          - name: https
            protocol: HTTPS
            port: 443
            tls:
              mode: Terminate
              certificateRefs:
              - kind: Secret
                name: my-tls-secret # Secret containing TLS certificate and key
            allowedRoutes:
              namespaces:
                from: Selector
                selector:
                  matchLabels:
                    expose-via-internet-gateway: "true"
        ```

      - **`HTTPRoute`**: Defines rules for routing HTTP traffic from a Gateway to backend Services. It's typically managed by application developers.

        ```yaml
        # httproute-example.yaml
        apiVersion: gateway.networking.k8s.io/v1
        kind: HTTPRoute
        metadata:
          name: my-app-route
          namespace: my-application # Routes are often in the application's namespace
          labels:
            expose-via-internet-gateway: "true" # Matches Gateway's allowedRoutes selector
        spec:
          parentRefs:
          - name: my-internet-gateway
            namespace: networking-infra # Points to the Gateway in its namespace
          hostnames:
          - "app.example.com"
          rules:
          - matches:
            - path:
                type: PathPrefix
                value: /login
            backendRefs:
            - name: login-service # Kubernetes Service name
              port: 8080
          - matches:
            - path:
                type: PathPrefix
                value: /
            backendRefs:
            - name: main-app-service
              port: 80
        ```

      - Other route types like `TCPRoute`, `TLSRoute`, and `GRPCRoute` handle different protocols.

  - **Pod-to-Pod Communication:** Every Pod gets its own unique IP address. Pods can communicate with all other Pods on all Nodes without NAT.

  - **Service Discovery:** Kubernetes provides DNS-based service discovery (e.g., a Service named `my-svc` in namespace `my-ns` can be reached at `my-svc.my-ns.svc.cluster.local`).

## 14.5: Storage in Kubernetes

- **Objective:** Learn how Kubernetes manages storage for applications, especially stateful ones.

- **Key Concepts:**

  - **Volumes:**

    - Volume lifecycle is tied to the Pod lifecycle (e.g., an `emptyDir` volume is created when a Pod is assigned to a Node, and exists as long as that Pod is running on that node; when a Pod is deleted, the `emptyDir` volume is deleted).

    - **Common Volume Types:**

      - `emptyDir`: A temporary directory that is created when a Pod is assigned to a Node.

      - `hostPath`: Mounts a file or directory from the host Node's filesystem into your Pod (tied to Node lifecycle).

      - Cloud provider specific volumes (e.g., `awsElasticBlockStore`, `gcePersistentDisk`, `azureDisk`).

  - **PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs):**

    - **PersistentVolume (PV):** A piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using StorageClasses. It is a resource in the cluster just like a Node.

    - **PersistentVolumeClaim (PVC):** A request for storage by a user. It is similar to a Pod. Pods consume Node resources and PVCs consume PV resources.

    - This abstraction decouples the provisioning of storage from its consumption, allowing Pods to request storage without needing to know the underlying storage details.

  - **StorageClasses:**

    - Provide a way for administrators to describe the "classes" of storage they offer. Different classes might map to quality-of-service levels, or to backup policies, or to arbitrary policies determined by the cluster administrators.

    - Enable dynamic provisioning of PVs: when a PVC requests a StorageClass, a PV can be automatically created to fulfill that claim.

    - **Example `StorageClass`:**

      ```yaml
      # storageclass-example.yaml
      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: standard-ssd # Name of the storage class
      provisioner: kubernetes.io/aws-ebs # Specific to AWS EBS, other providers have different provisioners
      parameters:
        type: gp3 # General Purpose SSD (gp3) volume type for AWS
        fsType: ext4 # Filesystem type
      reclaimPolicy: Retain # Or Delete. Retain means the PV is kept after PVC is deleted.
      allowVolumeExpansion: true
      mountOptions:
        - debug
      volumeBindingMode: Immediate # Or WaitForFirstConsumer
      ```

    - Example PersistentVolumeClaim (PVC):

      > This PVC requests storage from the standard-ssd StorageClass defined above.

      ```yaml
      # pvc-example.yaml
      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: my-app-pvc # Name of the PVC
        namespace: my-application
      spec:
        accessModes:
          - ReadWriteOnce # Can be mounted as read-write by a single node
                          # Other modes: ReadOnlyMany, ReadWriteMany, ReadWriteOncePod
        resources:
          requests:
            storage: 10Gi # Request 10 GiB of storage
        storageClassName: standard-ssd # Requests storage from this StorageClass
      ```

    - Example Pod using the PVC:

      > This Pod mounts the volume claimed by my-app-pvc into one of its containers.

      ```yaml
      # pod-with-pvc-example.yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: my-app-pod
        namespace: my-application
      spec:
        containers:
          - name: my-app-container
            image: nginx:latest # Example image
            ports:
              - containerPort: 80
            volumeMounts:
              - name: my-app-storage # Name of the volumeMount, must match a volume name below
                mountPath: /usr/share/nginx/html # Path inside the container where the volume is mounted
        volumes:
          - name: my-app-storage # Name of the volume, referenced by volumeMounts
            persistentVolumeClaim:
              claimName: my-app-pvc # Name of the PVC to use
      ```

## 14.6: Configuration Management

- **Objective:** Understand how to manage application configuration and sensitive data in Kubernetes.

- **Key Concepts:**

  - **ConfigMaps:**

    - Used to store non-confidential configuration data in key-value pairs.

    - Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume.

    - Decouples configuration from container images, making applications more portable.

  - **Secrets:**

    - Used to store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys.

    - Storing confidential information in a Secret is safer and more flexible than putting it verbatim in a Pod definition or in a container image.

    - Can be mounted as data volumes or exposed as environment variables to containers in a Pod.

    - Data is stored base64-encoded by default in `etcd`, but additional encryption at rest for `etcd` is recommended for production.

  - **Updating ConfigMaps and Secrets in Pods:**

    - When a ConfigMap or Secret is updated, Pods using it as **environment variables** will _not_ automatically see the changes. A Pod restart (e.g., by deleting the Pod and letting its controller recreate it) is required to pick up new environment variable values.

    - When a ConfigMap or Secret is mounted as a **volume** (e.g., as files), the data mounted into the Pod is updated periodically by the `kubelet` (the exact delay can vary but is typically within a minute or two). However, the application running inside the container needs to be able to detect and reload the updated configuration files from the mounted volume. If the application doesn't support live reloading, a Pod restart might still be necessary for the application to use the new values.

## 14.7: Advanced Object Concepts (Finalizers, Owner References, Garbage Collection)

- **Objective:** Understand how Kubernetes manages the lifecycle and cleanup of objects, particularly through finalizers and garbage collection.

- **Key Concepts:**

  - **Finalizers:**

    - Key-value annotations that signal to Kubernetes that specific cleanup actions must be performed _before_ an object can be fully deleted.

    - When an object with a finalizer is marked for deletion, the `deletionTimestamp` is set, but the object remains until all its finalizers are removed by their respective controllers.

    - Used by controllers to release external resources (e.g., a cloud load balancer associated with a Service, or an EBS volume associated with a PV) or perform other pre-deletion tasks.

    - **Example A (PV Protection):** A `kubernetes.io/pv-protection` finalizer on a PersistentVolume (PV) prevents it from being deleted if it's still bound to a PersistentVolumeClaim (PVC). The PV controller removes this finalizer only after the PV is no longer bound.

    - **Example B (Namespace Deletion):** When a Namespace is deleted, it typically has a finalizer like `kubernetes.io/namespace`. The namespace controller uses this to ensure all resources within that namespace (Pods, Services, Deployments, etc.) are deleted before the Namespace object itself is removed from the system. If some resources cannot be deleted (e.g., due to other finalizers on them), the Namespace will remain in a "Terminating" state.

  - **Owner References and Garbage Collection:**

    - Kubernetes uses owner references to manage the lifecycle of dependent objects. When an owner object is deleted, its dependent objects (those with an owner reference pointing to it) can be automatically garbage collected.

    - **Cascading Deletion:**

      - **Foreground Cascading Deletion:** The owner object remains in a "deletion in progress" state until all its dependents are deleted.

      - **Background Cascading Deletion:** The owner object is deleted immediately, and the garbage collector deletes the dependents in the background.

      - **Orphan Dependents:** If `propagationPolicy` is set to `Orphan`, dependent objects are not deleted when the owner is deleted.

    - `metadata.ownerReferences` field in an object specifies its owner(s). It includes the `apiVersion`, `kind`, `name`, and `uid` of the owner.

    - `blockOwnerDeletion`: A boolean in `ownerReferences` that, if true, prevents the owner from being deleted if this dependent object still exists.

**14.8: Common `kubectl` Commands**

- **Objective:** Learn the basic `kubectl` commands for interacting with a Kubernetes cluster.

- **Key Concepts:**

  - `kubectl` is the primary command-line tool for running commands against Kubernetes clusters.

  - **Syntax:** `kubectl [command] [TYPE] [NAME] [flags]`

  - **Common Commands:**

    - `kubectl get <pods|services|deployments|nodes|etc.>`: List resources.

      - Flags: `-n <namespace>`, `-o wide`, `-o yaml`, `-l <label_selector>`

    - `kubectl describe <pod|service|etc.> <resource_name>`: Show detailed information about a resource.

    - `kubectl create -f <filename.yaml>`: Create resources from a file or stdin.

    - `kubectl apply -f <filename.yaml>`: Apply a configuration to a resource by filename or stdin (declarative, preferred for updates).

    - `kubectl delete <pod|service|etc.> <resource_name>` or `kubectl delete -f <filename.yaml>`: Delete resources.

    - `kubectl logs <pod_name> [-c <container_name>]`: Print the logs for a container in a pod.

      - Flags: `-f` (follow), `-p` (previous instance).

    - `kubectl exec -it <pod_name> -- /bin/bash` (or `sh`): Execute a command in a container (interactive terminal).

    - `kubectl debug <pod_name | node_name> [-c <container_name>] [--image=<debug_image>] -- /bin/bash`: Debug running Pods or Nodes by creating a temporary debug container or copying the Pod with modifications.

    - `kubectl config view`: View current kubeconfig settings.

    - `kubectl config use-context <context_name>`: Switch contexts.

  - **Working with YAML Manifests:** Understanding the structure of Kubernetes YAML files for defining objects.

## 14.9: Helm Basics

- **Objective:** Get an introduction to Helm as a package manager for Kubernetes.

- **Key Concepts:**

  - **Basic Helm Commands:**

    - `helm repo add <repo_name> <repo_url>`: Add a chart repository.

    - `helm repo update`: Update information of available charts locally from chart repositories.

    - `helm search repo <keyword>`: Search repositories for a keyword in charts.

    - `helm install <release_name> <chart_name>` (e.g., `helm install my-nginx bitnami/nginx`): Deploy a chart.

    - `helm list`: List releases.

    - `helm upgrade <release_name> <chart_name>`: Upgrade a release.

    - `helm uninstall <release_name>`: Uninstall a release.

    - `helm template <release_name> <chart_name>`: Locally render templates.

    - `helm get manifest <release_name>`: Display the manifest for a named release (shows all Kubernetes resources deployed by the chart).
