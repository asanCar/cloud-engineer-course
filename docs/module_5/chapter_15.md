# Chapter 15: Introduction to Amazon EKS

**Estimated Time:** 10 hours

**Objective:** Understand Amazon EKS architecture, its benefits, key components, and the initial steps for setting up and interacting with an EKS cluster.

## 15.1: EKS Architecture Deep Dive

- **Objective:** Understand the fundamental architecture of Amazon EKS, including its managed components, data plane options, and how it integrates with AWS.

- **Key Concepts:**

  - **Managed Control Plane:**

    - AWS provisions, scales, and manages the Kubernetes control plane components (`kube-apiserver`, `etcd`, `kube-scheduler`, `kube-controller-manager`).

    - This includes ensuring high availability by default (multi-AZ deployment of control plane instances) and automated patching/upgrades of the control plane.

    - You interact with the control plane via the Kubernetes API endpoint provided by EKS.

    - `etcd` data is automatically encrypted by AWS.

  - **Data Plane Options (Worker Nodes):** Where your Pods run. You have several choices:

    - **Self-Managed EC2 Nodes:**

      - You provision and manage EC2 instances that register with the EKS control plane.

      - Full control over instance types, AMIs, operating systems, and patching.

      - Requires more operational overhead for node management, scaling, and upgrades.

      - EKS provides optimized AMIs with necessary components (kubelet, container runtime, AWS IAM Authenticator).

      - **Best for scenarios requiring:** Maximum control over node configuration, custom AMIs not easily supported by Managed Node Groups, specific kernel modules, or compliance requirements dictating full OS control. Also suitable if you have existing complex EC2 management automation.

    - **Managed Node Groups:**

      - Automates the provisioning and lifecycle management of EC2 worker nodes.

      - EKS handles patching, updates, and graceful termination of nodes.

      - Supports custom AMIs, various instance types, and auto-scaling group configurations.

      - Simplifies node operations significantly.

      - **Best for scenarios requiring:** A balance of control and automation. Ideal for most workloads where you want to offload node patching and updates to AWS but still need EC2 instance flexibility (e.g., specific instance types, GPU support, custom launch templates for some configurations). This is often the recommended default for EC2-based workloads.

    - **AWS Fargate:**

      - A serverless compute engine for containers.

      - You don't manage any underlying EC2 instances; AWS provisions and manages the infrastructure per Pod.

      - Pods run in their own isolated compute environment.

      - Ideal for applications with spiky workloads or when you want to minimize infrastructure management.

      - Pricing is based on vCPU and memory resources consumed by your Pods.

      - Fargate Profiles are used to specify which Pods (based on namespace and labels) should run on Fargate.

      - **Best for scenarios requiring:** Minimal operational overhead for nodes, applications with variable or unpredictable load (e.g., batch jobs, APIs with infrequent traffic), or when you want per-Pod resource isolation without managing node pools. Good for microservices that can scale independently.

  - **VPC Integration and Networking Considerations:**

    - EKS clusters run within your Amazon Virtual Private Cloud (VPC).

    - The AWS VPC CNI (Container Network Interface) plugin is used by default, allowing Pods to get IP addresses directly from your VPC subnets. This enables native VPC networking capabilities for Pods.

    - Control plane ENIs (Elastic Network Interfaces) are provisioned in your VPC subnets for communication between the control plane and worker nodes.

    - Understanding subnet requirements (public and private), security group configurations, and NACLs is crucial for cluster security and operation.

    - **Subnet Tagging for Load Balancers:** For the AWS Load Balancer Controller to automatically discover and use subnets for provisioning Application Load Balancers (ALBs) or Network Load Balancers (NLBs), specific tags are required:

      - Public subnets (for internet-facing load balancers) need the tag: `kubernetes.io/role/elb` with value `1`.

      - Private subnets (for internal load balancers) need the tag: `kubernetes.io/role/internal-elb` with value `1`.

      - All subnets used by the EKS cluster (both public and private that will host worker nodes or load balancers) must be tagged with `kubernetes.io/cluster/<cluster-name>` with a value indicating their lifecycle management:

        - `shared`: The subnet is used by the cluster but also by other AWS resources. It will not be deleted when the EKS cluster is deleted.

        - `owned`: The subnet is exclusively used by this EKS cluster. Tools like `eksctl` will delete these subnets when the cluster is deleted.

## 15.2: Creating an EKS Cluster

- **Objective:** Learn how to provision a new EKS cluster using `eksctl`.

- **Key Steps & Considerations:**

  - **Using `eksctl create cluster`:**

    - The simplest way to get a cluster up and running.

    - Example basic command:

            ```
            eksctl create cluster \
                --name my-eks-cluster \
                --version 1.29 \
                --region us-west-2 \
                --nodegroup-name standard-workers \
                --node-type t3.medium \
                --nodes 2 \
                --nodes-min 1 \
                --nodes-max 3 \
                --managed # For managed node groups            
            ```

  - **Key Cluster Configuration Options:**

    - `--name <cluster-name>`: A unique name for your EKS cluster.

    - `--version <k8s-version>`: The Kubernetes version for the control plane (e.g., 1.29, 1.28).

    - `--region <aws-region>`: The AWS region where the cluster will be created.

    - `--vpc-private-subnets <subnet-ids>` & `--vpc-public-subnets <subnet-ids>`: Specify existing subnets (if not, `eksctl` can create a new VPC).

    - `--nodegroup-name <name>`: Name for the initial node group.

    - `--node-type <instance-type>`: EC2 instance type for worker nodes.

    - `--nodes <count>`: Desired number of nodes.

    - `--nodes-min <count>` & `--nodes-max <count>`: For autoscaling node groups.

    - `--managed`: Creates an EKS Managed Node Group. If omitted (or `--managed=false`), it creates self-managed nodes with an Auto Scaling Group.

    - `--fargate`: Creates a Fargate-only cluster or adds a Fargate profile.

    - Cluster configuration can also be defined in a YAML file and passed to `eksctl create cluster -f cluster.yaml`.

  - **Cluster Provisioning Time:** Creating an EKS cluster can take 10-20 minutes or more.

  - **Verification:**

    - Check `eksctl` output and AWS CloudFormation console for status.

    - Once complete, use `kubectl get svc` (to see Kubernetes service) and `kubectl get nodes` (after configuring kubectl).

## 15.3: Connecting to Your EKS Cluster

- **Objective:** Configure `kubectl` to communicate with your newly created EKS cluster.

- **Key Steps:**

  - **Updating `kubeconfig`:**

    - The `kubeconfig` file (`~/.kube/config` by default) stores cluster connection information and authentication details.

    - Use the AWS CLI command:

            ```
            aws eks update-kubeconfig --region <your-region> --name <your-cluster-name>
            ```

            Example: `aws eks update-kubeconfig --region us-west-2 --name my-eks-cluster`

    - `eksctl` often does this automatically after successful cluster creation if AWS CLI is configured.

    - This command typically uses the AWS IAM Authenticator for Kubernetes (or `aws eks get-token` for newer versions) to generate temporary credentials for `kubectl`.

  - **Verifying Cluster Connectivity:**

    - After updating `kubeconfig`, test the connection:

            ```
            kubectl get nodes
            kubectl cluster-info
            ```

## 15.4: EKS Pricing and Cost Management

- **Objective:** Understand the pricing model for EKS and how to manage costs.

- **Key Components of EKS Costs:**

  - **Control Plane Cost:**

    - EKS charges an hourly rate per cluster for the managed control plane.

  - **Data Plane Costs (Worker Nodes & Resources):**

    - **EC2 Instances (Self-Managed or Managed Node Groups):** You pay standard EC2 prices for the instances you run as worker nodes.

    - **AWS Fargate:** You pay for the vCPU and memory resources consumed by your Pods running on Fargate, billed per second with a minimum charge.

    - **EBS Volumes:** If using PersistentVolumes backed by EBS, you pay standard EBS pricing.

    - **Load Balancers:** If you use Services of type `LoadBalancer`, you pay for the AWS Elastic Load Balancers (Classic, Network, or Application) provisioned.

    - **Data Transfer:** Standard AWS data transfer charges apply (e.g., inter-AZ, out to internet).

    - **Other AWS Services:** Costs for CloudWatch (logs, metrics), ECR (image storage), etc.

  - **Tips for Cost Optimization:**

    - Choose appropriate EC2 instance types and sizes for your workloads (right-sizing).

    - Utilize EC2 Spot Instances for fault-tolerant workloads in Managed Node Groups or with Karpenter to significantly reduce EC2 costs.

    - Implement cluster autoscaling (Cluster Autoscaler or Karpenter) and Horizontal Pod Autoscaler to scale nodes and Pods based on demand.

    - Use AWS Fargate for suitable workloads to avoid paying for idle EC2 capacity.

    - Monitor costs using AWS Cost Explorer, set up budgets and alerts.

    - Clean up unused resources (EBS volumes, load balancers, old clusters).

    - Consider Reserved Instances or Savings Plans for EC2 compute if you have predictable long-term usage.
