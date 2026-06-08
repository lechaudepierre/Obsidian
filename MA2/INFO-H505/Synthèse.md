## Cloud Concepts

Cloud computing is the on-demand delivery of compute, storage, databases, and other IT resources over the internet with pay-as-you-go pricing, rather than buying and maintaining physical data centers. Its core advantages over on-premises models include trading large up-front capital expenses for variable operating expenses, benefiting from the economies of scale of large providers, and stopping guessing about capacity since resources can be provisioned and released as demand changes. Agility, the speed at which resources can be created and discarded, lets organizations experiment cheaply and focus on their core business instead of undifferentiated infrastructure work.

Cloud Computing provides a simple way to access servers, storage, databases, and a broad set of application services over the internet. You own the network-connected hardware required for these services and the cloud provider provisions what you need. (True/False) False.

How do economies of scale help customers moving to cloud computing from on-premises computing? Customers can achieve lower variable costs and scale infrastructure beyond what's possible on-premises.

How does cloud computing improve a company's ability to provision resources to meet capacity demands compared to on-premises computing? Cloud resources can scale up or down based on demand.

Which of these is NOT a benefit of cloud computing over on-premises computing? Pay for racking, stacking, and powering services.

Which of the following are advantages of cloud computing for a company moving from a traditional on-premises computing model? (Select TWO) Resources can be created, scaled up, scaled down, or destroyed based on demand, The company can focus less on infrastructure and focus more on differentiating the business.

What does agility in cloud computing refer to? The speed at which resources can be provisioned and deprovisioned.

## Service Models

Cloud services are commonly grouped into three models that differ in how much of the stack the provider manages. With Infrastructure as a Service (IaaS) the customer controls operating systems, storage, and applications while the provider supplies the underlying compute and network, giving the most control and the closest fit to traditional on-premises management. Platform as a Service (PaaS) removes the need to manage operating systems and runtime, letting developers focus on deploying code, while Software as a Service (SaaS) delivers a finished application that the provider runs entirely. On AWS, managed offerings such as Elastic Beanstalk illustrate the PaaS approach by handling provisioning and deployment for the developer.

Which statement is an advantage of the platform as a service (PaaS) cloud service model? PaaS avoids the need to manage operating systems.

Which cloud service model provides the most control over the computing environment? Infrastructure as a Service (IaaS).

Which cloud service model eliminates the need for operating system maintenance and patches by the user, compared to on-premises servers? Platform as a Service (PaaS).

What is the service provided by AWS that enables developers to easily deploy and manage applications in the cloud? (Select the best answer) AWS Elastic Beanstalk.

## Regions, AZ & Edge

AWS organizes its global infrastructure into Regions, which are separate geographic areas, each containing multiple Availability Zones. An Availability Zone is one or more discrete data centers with redundant power and networking, isolated for fault tolerance yet linked to the other zones in the Region through low-latency, high-speed private connections. Choosing a Region close to users reduces latency and can help meet compliance requirements, while content delivery networks such as Amazon CloudFront use edge locations and regional edge caches to cache frequently requested files nearer to end users.

Which of these statements about Availability Zones is NOT true? (Select the best answer) A data center can be used for more than one availability zone.

Availability zones within a Region are connected through low-latency links. (True/False) True.

What is the relationship between AWS Regions, Availability Zones, and data centers? Each Region has locations called Availability Zones. Each Availability Zone has data centers.

Which statement about AWS Regions is true? Using a Region as close as possible to users can reduce latency.

Which statement about edge locations is true? Amazon CloudFront uses edge locations and Regional edge caches to deliver content with lower latency.

Which statement accurately describes a content delivery network (CDN)? A CDN caches frequently requested files.

## EC2 & Compute

Amazon EC2 (Elastic Compute Cloud) provides resizable virtual server capacity in the cloud, giving full control over the operating system, configuration, and application stack. Access is secured with SSH key pairs and traffic is governed by security groups, which act as virtual firewalls defining the rules for inbound and outbound traffic to an instance. EC2 offers several pricing models: On-Demand for uninterrupted, short-term or unpredictable workloads, Reserved Instances for steady long-term use, and Spot Instances for the lowest cost on interruptible, fault-tolerant jobs, while Dedicated Instances guarantee that hardware is not shared with other customers.

A developer is testing a prototype on EC2. The instances are terminated after testing, but the application requires uninterrupted compute while processing. Which type of EC2 instance pricing meets the need at the lowest cost? On-demand instance.

A company has a set of big data processing jobs in Amazon SQS that need a lot of compute. Which EC2 instance pricing model would meet the need at the lowest possible cost? Spot instance.

Which Amazon EC2 feature ensures your instances will not share a physical host with instances from any other AWS customer? (Select the best answer) Dedicated Instances.

What is Amazon EC2 (Elastic Compute Cloud)? A web service that provides resizable compute capacity in the cloud.

A company wants complete control over its server's configurations, operating system (OS), and the application software stack. Which AWS compute service should they choose? Amazon EC2.

What happens when you stop an EC2 instance? The instance is paused, but its data on the EBS volume persists.

How can you securely connect to an EC2 instance using SSH? By using a key pair generated by AWS during instance creation.

What role do security groups play in managing access to EC2 instances? Security groups provide a set of rules to control traffic to or from an instance.

## Auto Scaling & ELB

Amazon EC2 Auto Scaling automatically adjusts the number of instances in a group to match demand, defined by a minimum size, maximum size, and desired capacity, and it adds value for both predictable and unpredictable workloads. A launch configuration or template specifies how new instances are built, including the Amazon Machine Image (AMI), instance type, and storage. Elastic Load Balancing complements Auto Scaling by distributing incoming traffic across healthy instances and performing health checks; among its types, the Network Load Balancer handles very high volumes of traffic at low latency at layer 4, while the Application Load Balancer routes based on request content at layer 7.

Which statement accurately describes how auto scaling is used? Auto scaling is useful for predictable workloads.

Which statement about AWS Auto Scaling is true? AWS Auto Scaling can be used to automatically scale Amazon DynamoDB tables and indexes.

Which of the following pieces of information MUST be configured for the EC2 instances that will be part of an Auto Scaling group? (Select TWO) EC2 instance type, ID of an Amazon Machine Image (AMI).

Which of the following are elements of an Auto Scaling group? (Choose three) Minimum size, Desired capacity, Maximum size.

Which of the following elements are used to create an Amazon EC2 Auto Scaling launch configuration? (Choose three) Amazon EBS volumes, Amazon Machine Image (AMI), Instance type.

What is an Auto Scaling group in Amazon EC2? A group of EC2 instances that automatically scale up or down based on demand.

How is Elastic Load Balancing (ELB) used with Amazon EC2 Auto Scaling? (Select TWO) ELB performs health checks on new EC2 instances that are added to the Amazon EC2 Auto Scaling group, ELB distributes traffic between EC2 instances in an Auto Scaling group.

Which scenario should be addressed with a network load balancer? A solution must load balance millions of requests per second while maintaining low latency.

## Scaling & Resilience

Scaling can be vertical, adding more CPU or memory to an existing server, or horizontal, adding more instances to share the load, with horizontal scaling being the typical cloud approach for handling growth. Resilience is described through related but distinct properties: elasticity is the ability to add or remove resources automatically as demand changes, high availability means a system stays operational with minimal downtime and intervention despite some degradation, and fault tolerance relies on built-in component redundancy so the system keeps running through failures. Required availability varies by use case, so non-critical workloads like batch processing may tolerate lower availability targets than transactional systems.

Which of the following best describes vertical scaling? Adding more resources (CPU, RAM) to an existing server.

What is a primary advantage of horizontal scaling? It allows for adding more instances to handle increased load.

Which of the following best describes a system that can withstand some measures of degradation, experiences minimal downtime, and requires minimal human intervention? (Select the best answer) Highly available.

___ means the infrastructure has built-in component redundancy and ___ means that resources dynamically adjust to increases or decreases in capacity requirements. Fault-tolerant, elastic and scalable.

What is a key component of a fault-tolerant cloud architecture? Redundant systems and components.

Elasticity in cloud computing is most closely associated with which of the following? On-demand resource provisioning.

Which statement best defines elasticity in cloud computing? The ability to add or remove resources as needed.

For which type of use case is it usually OK to have 2 9s of availability (99%)? Batch processing.

## Well-Architected & CAF

The AWS Well-Architected Framework captures best practices across pillars such as reliability, performance efficiency, security, cost optimization, operational excellence, and sustainability. Reliability design principles favor replacing one large resource with multiple smaller ones and distributing requests across them, while performance efficiency encourages using serverless architectures and democratizing advanced technologies so teams can adopt them easily. The separate AWS Cloud Adoption Framework (CAF) organizes cloud transformation into perspectives; its business perspective helps stakeholders build a strong business case for adoption and prioritize initiatives that align IT with business goals.

Which statement reflects a design principle of the Reliability pillar of the AWS Well-Architected Framework? Replace one large resource with multiple, smaller resources, and distribute requests across these smaller resources.

Which design principles are recommended when considering performance efficiency? (Choose two) Use serverless architecture, Democratize advanced technologies.

Which statement describes the business perspective of the AWS Cloud Adoption Framework? Stakeholders can create a strong business case for cloud adoption and prioritize cloud adoption initiatives.

## Storage (S3/EBS/EFS)

AWS offers distinct storage types for different needs. Amazon EBS provides persistent block storage volumes attached to a single EC2 instance and backed up via snapshots, whereas instance store offers fast but ephemeral block storage that is lost when the instance stops. Amazon EFS is a shared file system that many instances across Availability Zones can mount simultaneously, and Amazon S3 is object storage for unstructured data, organized into Region-specific buckets. S3 storage classes trade cost against access patterns, from Standard for frequently used data, to Intelligent-Tiering for unpredictable access, to Glacier for long-term archival, and lifecycle policies automate moving objects between classes over time.

Which scenario is a good fit for Amazon Redshift? A company needs a data warehouse to support analytics applications.

A developer wants to use Amazon Elastic Block Store (Amazon EBS) for their application. What action should they take? Attach the Amazon EBS volume to an Amazon EC2 instance.

A developer needs temporary block storage for cache data on an EC2 instance. Which option should they choose? EC2 instance store.

Which type of storage volume is designed for temporary data that persists only as long as the associated EC2 instance is running? Instance store volume.

What is the difference between Amazon EBS and instance store volumes? EBS volumes are persistent, while instance store volumes are ephemeral.

Amazon EBS offers various volume types. Which volume type provides the highest performance for applications requiring sustained IOPS performance, measured in thousands? Provisioned IOPS SSD (io2).

What is the purpose of Provisioned IOPS (PIOPS) volumes in Amazon EBS? To optimize performance for mission-critical, I/O-intensive workloads.

You can use Amazon Elastic File System (Amazon EFS) to: (Select the best answer) Implement storage for Amazon EC2 instances that multiple virtual machines can access at the same time.

Which scenario is a good fit for Amazon Elastic File System (Amazon EFS) storage? A company needs to give all EC2 instances in its VPC read and write access to a network file system (NFS).

When you create a bucket in Amazon S3, it is associated with a specific AWS Region. (True/False) True.

Which scenario describes a good use case for Amazon S3 Standard storage? Host website images.

A company needs to store long-lived data. They need the data to be available immediately, but access patterns are unpredictable. Which Amazon S3 storage class would be most cost effective? Amazon S3 Intelligent-Tiering.

A company uploads PDF forms to Amazon S3 that must be retained for 1 year. The forms are rarely accessed after 1 week but must be available within 1 day when requested. What lifecycle policy is the most cost effective? Move objects from Amazon S3 Standard to Amazon S3 Glacier after 7 days. Delete them after 365 days.

Which of the following can be used as a storage class for an S3 object lifecycle policy? (Choose three) S3 Infrequent Access, S3 Glacier, S3 Standard Access.

What feature of Amazon S3 allows you to automatically move data between different storage classes based on preset policies? Lifecycle Policies.

Which feature of cloud storage services allows for automated data migration between different storage tiers based on access patterns? Lifecycle management policies.

Which use case is Amazon S3 Glacier best suited for? Long-term archival of infrequently accessed data.

Which of the following best describes the term "object storage"? Storage for unstructured data as discrete units.

What is the primary benefit of using Object Locking or Write Once Read Many (WORM) storage in cloud storage services? Data immutability for compliance purposes.

Which of the following is a potential challenge of implementing geo-redundancy in cloud storage? Increased latency.

## Databases

AWS provides managed database services suited to different data shapes and access patterns. Amazon RDS runs relational databases where AWS handles operating system and database patching, leaving application optimization to the customer, and Multi-AZ deployments add high availability through a standby replica. Amazon DynamoDB is a NoSQL key-value and document store well suited to unstructured metadata, session state, and very high concurrency, using partition and sort keys to retrieve items efficiently. Amazon Redshift is a data warehouse built for analytics over large datasets, and selecting a database means weighing factors such as data size, query frequency, access patterns, and availability needs.

In Amazon DynamoDB, what does the query operation enable you to do? (Select the best answer) All the Above.

What is an attribute in a DynamoDB table? A data element that doesn't need to be broken down further.

You are designing an e-commerce web application that will scale to hundreds of thousands of concurrent users. Which database technology is best suited to hold the session state? (Select the best answer) Amazon DynamoDB.

A company has an e-commerce site that requires storage and retrieval of unstructured customer metadata to support one of its microservices. Which database option is best suited to store this data? Amazon DynamoDB.

What should you consider when choosing a database type? (Select the best answer) All of the Above.

Which option is a company's responsibility when running Amazon RDS? Application optimization.

Which feature of Amazon RDS should a company configure to enable high availability? Multi-AZ deployment.

## Networking & VPC

An Amazon Virtual Private Cloud (VPC) is a logically isolated section of the AWS Cloud where you launch resources, and it can span the Availability Zones within a Region. When a VPC is created, a main route table is provided by default. Subnets are public or private: a public subnet routes traffic to the internet through an internet gateway, while private subnets have no direct internet access and instead use a NAT gateway for outbound connections. Underlying these capabilities, software-defined networking decouples network control from the physical hardware, enabling this kind of flexible virtual networking.

Which option describes a capability of Amazon virtual private clouds (VPCs)? Can span Availability Zones.

What happens when you use Amazon Virtual Private Cloud (Amazon VPC) to create a new VPC? (Select the best answer) A main route table is created by default.

Private subnets have direct access to the internet. (True/False) False.

A network administrator wants to configure a public subnet and route incoming and outgoing traffic to and from an EC2 instance in the public subnet to the public internet. Which VPC feature should they use? An internet gateway.

Which technology enables network virtualization by decoupling network resources from underlying hardware? Software-Defined Networking (SDN).

## Security & IAM

AWS operates on a shared responsibility model: AWS secures the infrastructure (security of the cloud) while customers secure what they put in it (security in the cloud), such as data encryption and security group configuration. Identity and Access Management (IAM) controls who can do what, using identity-based policies attached to users, groups, or roles, and resource-based policies including access control lists attached to resources. Roles allow temporary, assumable access, including cross-account scenarios, and TLS with services like AWS Certificate Manager protects data in transit. For auditing, AWS CloudTrail logs API activity and account access, while AWS Config tracks changes to resource configurations over time.

In the shared responsibility model, which of the following are examples of "Security in the cloud"? (Choose two) Encryption of the data at rest and data in transit, Security group configurations.

Which of the following statements about identity and access management (IAM) policies are accurate? (Select TWO) Access control lists (ACLs) are a form of resource-based policies, Identity-based policies are attached to a user, group, or role.

When creating an AWS IAM policy, what are the two types of access that can be granted to a user? (Choose two) Programmatic Access, AWS Management Console Access.

An AWS account administrator wants to grant temporary cross-account access that allows external users access to specific resources within their own account. Which action aligns with the best practice of using temporary sessions? Create an IAM role that can be assumed by external users and grant it permissions to the specific resources.

Which of the following statements about securing data in transit are true? (Select TWO) Transport layer security (TLS) certificates can be managed using AWS Certificate Manager (ACM), Transport layer security (TLS) provides encryption of data in transit.

There is an audit at your company and they need to have a log of all access to AWS resources in the account. Which service can assist in providing these details? (Select the best answer) AWS CloudTrail.

A company must produce reports of any changes to its EC2 instance settings. Which AWS service should they use? AWS Config.

## Organizations, Support & Billing

AWS Organizations lets a company consolidate and centrally manage many AWS accounts, automate account creation through APIs, group accounts to attach policies, and benefit from volume discounts via consolidated billing. AWS Support comes in tiers, Basic, Developer, Business, and Enterprise, covering both experimental and business-critical accounts, and Trusted Advisor offers recommendations on configuring resources, including alerts such as missing multi-factor authentication. For cost management, the Pricing Calculator estimates costs before deployment, Cost Explorer analyzes historical spend by dimensions like instance type, and total cost of ownership analyses weigh factors such as the amount of storage and number of servers to migrate.

Which of the following statements about how a company would use AWS Organizations are accurate? (Select TWO) A company can consolidate and centrally manage multiple AWS accounts, A company can benefit from volume discounts from consolidated billing.

What are benefits of using AWS Organizations? (Choose two) Simplifies automating account creation and management by using APIs, Provides the ability to create groups of accounts and then attach policies to a group.

AWS Organizations enables you to consolidate multiple AWS accounts so that you centrally manage them. (True/False) True.

Which statement accurately describes how customers can use AWS Support? Customers can get AWS Support for both experimental non-production accounts and for business-critical production accounts.

What are the four support plans offered by AWS Support? (Select the best answer) Basic, Developer, Business, Enterprise.

What type of alert might be provided by AWS Trusted Advisor? An alert that multi-factor authentication (MFA) isn't activated on an AWS account.

How does AWS Trusted Advisor assist a company getting started with AWS? Trusted Advisor provides recommendations on configuring your AWS resources.

Which statement accurately describes AWS pricing? Volume-based discounts are available when usage increases (on some services).

What AWS tool lets you explore AWS services and create an estimate for the cost of your use cases on AWS? (Select the best answer) AWS Pricing Calculator.

A cloud practitioner wants to visualize their AWS costs per EC2 instance type for the past 3 months. Which AWS tool or feature should they use? AWS Cost Explorer.

Which of the following factors are considered in calculating the total cost of ownership (TCO) for the AWS Cloud? (Select TWO) The amount of storage that needs to be migrated to the cloud, The number of servers that need to be migrated to the cloud.

## Virtualization

Virtualization abstracts physical resources into virtual ones, and it takes several forms: server virtualization partitions one physical machine into multiple virtual servers running their own operating systems, storage virtualization pools physical devices into a single logical unit, application virtualization decouples applications from the underlying OS for portability, and desktop virtualization delivers hosted virtual desktops to users. A Virtual Machine Monitor, or hypervisor, manages access to hardware such as memory. Full virtualization runs unmodified guest operating systems, while paravirtualization modifies the guest for higher performance and efficiency. Virtual machines can be migrated between hosts, and concepts like Java byte-code show how a portable intermediate layer achieves platform independence.

What is server virtualization? Partitioning a physical server into multiple virtual servers that can run different operating systems and applications.

What is the purpose of application virtualization in cloud computing? To decouple applications from underlying operating systems for portability and flexibility.

What is storage virtualization? Pooling physical storage devices and presenting them as a single virtual storage unit.

What is desktop virtualization? Providing users with access to virtual desktop instances hosted in the cloud.

Which of the following is NOT a type of virtualization commonly used in cloud computing? DNS virtualization.

How does Paravirtualization differ from Full Virtualization in terms of performance? Paravirtualization offers higher performance compared to Full Virtualization.

In which scenario is Paravirtualization often preferred over Full Virtualization? When maximum performance and resource efficiency are critical.

Which component is responsible for managing memory access in Full-Virtualization setups? Virtual Machine Monitor (VMM).

Which of the following statements about virtual machines (VMs) is true? VMs can be easily migrated between different host machines.

Which part of a Java program's execution is platform-independent? Byte-code.

## Containers & Orchestration

Containers package an application with its dependencies into a lightweight, portable unit that runs directly on the host operating system kernel, making them faster and more resource-efficient than virtual machines, though VMs provide stronger isolation between workloads. Container isolation limits the blast radius if one container is compromised. Orchestration systems manage containers at scale: Kubernetes introduced the Pod as the unit for grouping containers and uses YAML files to declare resources and configurations, building on earlier Google systems like Borg and Omega that focused on efficient resource utilization. On AWS, Fargate offers a serverless way to run containers without managing the underlying servers.

What role do YAML files play in Kubernetes deployment? Configuring and deploying Kubernetes resources such as Pods, Deployments, and Services.

What is the primary role of YAML files in Kubernetes? Describing resources and configurations.

What is the benefit of linking only 2-3 containers together on a network in bridge mode? It improves network security.

What is OrbStack? A GUI software for Docker management.

Which of the following is NOT a container orchestration system? Docker Swarm.

What was one of the main goals of Borg and Omega at Google? Efficient resource utilization.

Which system introduced the concept of Pods for managing groups of containers? Kubernetes.

Which technology provides better isolation between applications running on the same host? Virtual Machines.

What is a characteristic feature of containers compared to virtual machines? Containers run directly on the host operating system kernel.

What is a container in cloud computing? A lightweight, portable, and self-contained environment for running applications.

If a container containing a web server gets hacked, what prevents easy access to the rest of the infrastructure? Container isolation.

What is AWS Fargate? A serverless way to launch containers.

## Serverless

Serverless computing lets developers run code without provisioning or managing servers, with the provider handling scaling and infrastructure. Function as a Service (FaaS) is the core model, executing individual functions in response to events and billed on a pay-per-use basis tied to the number of invocations and their execution duration. Because functions are short-lived and stateless, serverless designs struggle with long-running tasks and maintaining state between invocations, so state is typically externalized to durable stores such as Amazon DynamoDB and coordinated through messaging services. Access is secured by applying fine-grained IAM policies to function execution.

What is Function as a Service (FaaS) in serverless computing? It allows developers to deploy and run individual functions in response to events.

What is the billing model for serverless computing services? Pay-per-use pricing based on the number of function invocations and execution duration.

What challenges do serverless architectures face regarding state management and durability? Inability to handle long-running tasks or maintain state between invocations.

How can you implement stateful communication between serverless functions in a scalable and reliable manner? By storing state information in serverless databases like Amazon DynamoDB.

How can you ensure secure access control and authorization in serverless applications? By implementing fine-grained IAM policies for function execution.

Which statement best describes the serverless computing model? It allows developers to focus on writing code without managing servers.

## ML, Deep Learning & AI

Running machine learning and deep learning in the cloud provides scalability and flexibility, on-demand access to specialized hardware, and pre-installed frameworks that speed up setup. Accelerators differ in purpose: GPUs parallelize training, and TPUs are purpose-built for high-performance deep learning training, though using multiple GPUs introduces challenges such as higher power consumption while reducing training time. Provider choice depends on cost, performance, and ease of setup, and some platforms integrate tightly with specific frameworks, such as Google Cloud with TensorFlow. Foundation models are pretrained on vast data with self-supervised learning and are adaptable to many tasks, and generative AI creates new content whereas predictive AI forecasts outcomes.

What is one of the main advantages of using cloud infrastructure for machine learning? Scalability and flexibility.

What is an important factor to consider when selecting a cloud provider for deep learning tasks? Cost, performance, and ease of setup.

What is a significant advantage of using cloud platforms for deep learning projects? Pre-installed deep learning frameworks.

Which deep learning chip is known for being optimized for high performance in training deep learning models? TPU.

Which of the following is a challenge when using multiple GPUs? Higher power consumption.

What is a key benefit of using multiple GPUs for deep learning? Reduced model training time.

Which cloud platform is known for its tight integration with TensorFlow? Google Cloud Platform (GCP).

Which of the following is NOT a feature of Foundation Models (FMs)? Task-specific and rigid.

What distinguishes Generative AI from Predictive AI? Generative AI creates new content or ideas, while Predictive AI forecasts outcomes.