# Intermediate Developer Course: Cloud & DevOps Focus

This course is designed for developers with some prior programming experience (Python or Go recommended) who want to deepen their skills in programming best practices, cloud computing (specifically AWS), system design, and container orchestration with Kubernetes (focusing on AWS EKS).

Target Audience: Intermediate Developers

Prerequisites: Basic understanding of programming (Python or Go), familiarity with Linux/Unix command line, basic Git knowledge.

**Course Index & Estimated Time Commitment:**

## **Module 1: Advanced Programming & Best Practices**

### **[Chapter 1: Language Deep Dive & Idiomatic Code](./module_1/chapter_1.md)**

- _Content:_ Review of intermediate concepts, advanced language features (e.g., decorators/interfaces, concurrency models), error handling, code style, naming conventions.

- _Estimated Time:_ 10 hours

### **Chapter 2: Environment, Dependencies & Build Tools**

- _Content:_ Virtual environments (venv/go modules), dependency management (pip/go mod), package structure, build processes.

- _Estimated Time:_ 5 hours

### **Chapter 3: Testing Strategies**

- _Content:_ Unit testing frameworks (e.g., `unittest`/`pytest` or Go's `testing`), mocking, test-driven development (TDD) principles, basic integration testing concepts.

- _Estimated Time:_ 8 hours

### **Chapter 4: Project - Refactoring & Enhancing an Existing Application**

- _Content:_ Apply learned concepts to refactor a moderately complex application, improve test coverage, and implement new features following best practices.

- _Estimated Time:_ 20 hours

## **Module 2: Essential Algorithms**

### **Chapter 5: Sorting Algorithms**

- _Content:_ Implementation, time/space complexity analysis of Bubble Sort, Insertion Sort, Selection Sort, Merge Sort, Quick Sort, Heap Sort. Understanding trade-offs.

- _Estimated Time:_ 12 hours

### **Chapter 6: Searching Algorithms & Hashing**

- _Content:_ Implementation, time/space complexity analysis of Linear Search, Binary Search. Introduction to hash tables, collision resolution strategies.

- _Estimated Time:_ 8 hours

## **Module 3: System Design Fundamentals**

### **Chapter 7: Core Concepts & Trade-offs**

- _Content:_ Scalability (vertical vs. horizontal), Availability, Reliability, Consistency (CAP Theorem), Latency, Performance, Monitoring.

- _Estimated Time:_ 10 hours

### **Chapter 8: System Building Blocks**

- _Content:_ Load Balancers, Databases (SQL vs. NoSQL, replication, sharding), Caching (strategies, Redis/Memcached), Message Queues (RabbitMQ/Kafka basics), Content Delivery Networks (CDNs).

- _Estimated Time:_ 15 hours

### **Chapter 9: Architectural Patterns**

- _Content:_ Monoliths vs. Microservices, API Gateways, Service Discovery, Stateless vs. Stateful services, Asynchronous processing.

- _Estimated Time:_ 12 hours

### **Chapter 10: System Design Case Studies**

- _Content:_ Walkthroughs of designing common systems (e.g., URL shortener, social media feed, e-commerce backend). Applying concepts and making design choices.

- _Estimated Time:_ 15 hours

## **Module 4: Cloud Computing with AWS & Boto3**

### **Chapter 11: AWS Core Services Overview**

- _Content:_ Introduction to key services: EC2 (Instances), S3 (Storage), VPC (Networking), IAM (Security), RDS (Databases), CloudWatch (Monitoring).

- _Estimated Time:_ 10 hours

### **Chapter 12: Programmatic AWS Interaction with Boto3 (Python)**

- _Content:_ Setting up Boto3, authentication (credentials, IAM roles), interacting with core services (EC2, S3, IAM) via the SDK.

- _Estimated Time:_ 15 hours

### **Chapter 13: Project - Cloud Automation Scripts**

- _Content:_ Develop Python scripts using Boto3 to automate common AWS tasks (e.g., launching/terminating EC2 instances based on tags, managing S3 bucket policies, creating IAM users/roles).

- _Estimated Time:_ 20 hours

## **Module 5: Kubernetes & AWS EKS**

### **Chapter 14: Kubernetes Fundamentals**

- _Content:_ Core concepts: Pods, Services, Deployments, ReplicaSets, Namespaces, ConfigMaps, Secrets, Persistent Volumes, Helm basics.

- _Estimated Time:_ 15 hours

### **Chapter 15: Introduction to Amazon EKS**

- _Content:_ EKS architecture (managed control plane, data plane options), creating an EKS cluster, `eksctl` basics.

- _Estimated Time:_ 10 hours

### **Chapter 16: EKS vs. Vanilla Kubernetes - Key Differences**

- _Content:_ Deep dive into networking (AWS VPC CNI), IAM integration (IAM Roles for Service Accounts - IRSA), Load Balancing (AWS Load Balancer Controller), Authentication/Authorization, Upgrades.

- _Estimated Time:_ 12 hours

### **Chapter 17: Advanced EKS Operations & Features**

- _Content:_ Managed Node Groups vs. Fargate, Cluster Autoscaling (**Cluster Autoscaler vs. Karpenter**), Monitoring & Logging integration (CloudWatch Container Insights), Security best practices on EKS.

- _Estimated Time:_ 15 hours

### **Chapter 18: Project - Deploying a Multi-Tier Application on EKS**

- _Content:_ Containerize an application, write Kubernetes manifests (Deployments, Services, Ingress), configure IRSA, deploy using `kubectl` or Helm, set up monitoring and auto-scaling (implementing **Karpenter**). Highlight EKS-specific configurations.

- _Estimated Time:_ 25 hours

**Total Estimated Course Time:** Approximately 240 - 250 hours (Studying 1-2 hours per day, the course would take approximately 120-250 days to complete)

_(Note: These are estimates and actual time may vary based on the individual's learning pace and prior experience.)_