# ☁️ Cloud Computing — Exam Recap

> A reading-oriented recap built from this year's QCM themes (S1–S7 + SDN) and the past-year AWS exams. Written as prose so the _why_ behind each answer sticks, not just the answer.

---

## 🧱 S1 — Virtualization & Docker

Virtualization is the foundation of cloud computing: it lets a provider take physical hardware and slice or pool it into flexible logical resources. There are several flavours. **Server virtualization** partitions one physical machine into multiple virtual servers, each able to run its own OS and applications. **Storage virtualization** pools several physical disks and presents them as a single logical unit; its earliest, "embryonic" form is **RAID**, which already abstracted multiple disks into one. **Network virtualization** abstracts routers and switches into software, **desktop virtualization (VDI)** delivers virtual desktop instances hosted in the cloud, and **application virtualization** decouples an app from the underlying OS so it can run portably across environments.

A key distinction is **full vs para-virtualization**. In full virtualization the guest OS is unmodified and the **Virtual Machine Monitor (VMM/hypervisor)** intercepts and manages privileged operations, including memory access. Para-virtualization modifies the guest OS so it cooperates with the hypervisor, avoiding the overhead of hardware emulation — which makes it **faster**, and the preferred choice when raw performance and resource efficiency matter most. The trade-off is that you can't run an unmodified OS.

VMs give strong **isolation**: a crash or security breach in one VM cannot reach the host or sibling VMs. **Containers** take a lighter approach — instead of bundling a full guest OS, they **share the host's OS kernel**, which is exactly why they're so lightweight and start fast. The downside is weaker isolation, and that explains a classic exam point: in public clouds, containers are often run _inside_ VMs so the provider still has a hardware-level security boundary between different customers sharing the same physical host.

On the tooling side, **Docker** packages an application and all its dependencies into a portable container image. Images live in a **container registry** (e.g. Docker Hub); `docker push` uploads an image to the registry and `docker pull` downloads one. **Docker Compose** lets you define and run several services together from a single configuration file. A security best practice that shows up in the QCM: never hardcode credentials in a Dockerfile or commit them — put them in a `.env` file kept out of version control. Finally, **OrbStack** is simply a GUI for managing Docker.

---

## ⎈ S2 — Kubernetes & Containers

Once you run containers at scale, manual management becomes impossible — that's the entire justification for an orchestrator like **Kubernetes**: it automates deployment, scaling, healing, and management across a cluster.

The **control plane** coordinates everything. The **API Server** is the central gateway through which all REST commands pass; **etcd** is the key-value store holding the cluster's state; the **Scheduler** decides which node a Pod runs on; and the **kubelet** is the agent on each worker node that actually runs containers. Kubernetes is **declarative and self-healing**: you describe a _desired state_ and it continuously reconciles reality to match it. If you manually delete a Pod managed by a Deployment, Kubernetes notices the drift and spins up a replacement. If an entire node crashes, it reschedules that node's Pods onto the remaining healthy ones.

A few terms worth knowing precisely. Setting an app to **3 replicas** means three identical instances run simultaneously, each in its own Pod, for availability and load sharing. A **Namespace** logically partitions one cluster into separate virtual environments for different teams or projects (it's organisational, not hardware isolation). **Services** expose Pods: `ClusterIP` is internal-only, `NodePort` opens a static port on each node, and `LoadBalancer` integrates with the cloud provider to expose an app to the public internet. For production you define resources in **YAML files** rather than ad-hoc `kubectl` commands, because YAML gives you a reproducible, version-controlled "source of truth" for the desired state.

For the history questions: **Borg and Omega** were Google's internal systems whose main goal was **efficient resource utilization**, and **Kubernetes** introduced the concept of the **Pod**. Watch the recurring trick question — **Docker Swarm is NOT** considered part of the Borg/Omega/Kubernetes orchestration lineage in this course's framing.

---

## 📡 S3 — IoT & Edge Computing

Modern IoT spreads computation across a **continuum**: endpoint devices, edge/fog nodes in the middle, and centralized cloud infrastructure at the top — rather than pushing everything to a hyperscale data center. Within this paradigm, "**things**" are not passive sensors; they act as **both data producers and active consumers** in a distributed mesh, generating readings and also receiving and acting on commands.

The link to **Big Data** comes from scale: massive numbers of concurrent sensors produce **high-velocity, high-volume** streams, which is the defining characteristic of Big Data here (not large multimedia files on individual devices). The main reason to push processing toward the edge rather than relying purely on the cloud is **latency and bandwidth**: many applications need sub-millisecond response times, and sending every raw reading to the cloud would saturate backhaul links. Doing so introduces its own **systemic challenges** — orchestrating services across many distributed nodes, securing a decentralized environment, and coping with heterogeneous hardware.

---

## 💾 S4 — Storage (DAS / SAN / NAS, RAID, EBS, S3)

The storage story starts with **DAS (Direct Attached Storage)**, where disks are tied directly to one server. Its limitation is that it creates **isolated silos** with no sharing between servers, which drove the development of networked storage. **SAN (Storage Area Network)** operates at the **block level** (it presents raw volumes, typically over iSCSI), while **NAS (Network Attached Storage)** operates at the **file level** using protocols like NFS or CIFS. **RAID** underpins much of this; **RAID 5** is one of the most used levels because it strikes a good balance between performance and redundancy through distributed parity (tolerating one disk failure). **Block-level virtualization** works by splitting physical disks into small chunks, organising them into logical blocks, and distributing those across disks. For big-data workloads, **HDFS** is designed for large-scale **batch** processing with high throughput, not low-latency random access.

On AWS, the two core block options contrast sharply. **EBS (Elastic Block Store)** is **persistent** network-attached block storage, while an **instance store** is **ephemeral** — its data exists only while the EC2 instance is running and is lost when the instance stops. Several operational rules recur in exams: an EBS volume and its instance must be in the **same Availability Zone**; you access EBS by **attaching it to an EC2 instance**; and after enlarging a volume you must **extend the filesystem** inside the OS for the space to be usable. **Snapshots** are stored in S3 and are **incremental** — only blocks changed since the last snapshot are saved, which is why a later snapshot of an unchanged volume is much smaller. A snapshot is not directly mountable: to recover, you **create a new EBS volume from it**, and you can target a _different_ AZ in the process. When you **stop** an instance, it's paused and its EBS data persists. Among EBS types, **Provisioned IOPS SSD (io2)** delivers the highest sustained IOPS for I/O-intensive workloads, `gp3` is general purpose, and `st1`/`sc1` are throughput-oriented and cold HDDs.

**Amazon S3** is object storage for **unstructured data as discrete units**, ideal for things like website images and static assets. Its storage classes form a cost/access spectrum: **Standard** → **Standard-IA** → **One Zone-IA** → **Glacier** (long-term archival of rarely accessed data). **Intelligent-Tiering** is the right choice when access patterns are unpredictable but data must be available immediately, since it moves objects automatically without retrieval delay. **Lifecycle policies** automate transitions between classes (and deletion) based on age — a common cost-optimization question (e.g. move to Glacier after 7 days, delete after 365). **Object Lock / WORM** (Write Once Read Many) provides **data immutability for compliance**. Related services worth distinguishing: **EFS** is a shared NFS file system multiple EC2 instances can mount at once; **Redshift** is a data warehouse for analytics (not a transactional DB); and **DynamoDB** is a NoSQL store well suited to unstructured data and session state at massive concurrency.

---

## ⚡ S5 — Serverless & AWS Lambda

Serverless lets developers run code **without provisioning or managing servers**, paying only for what runs — billing is **per invocation and per execution duration**, with no idle cost. **AWS Lambda** is the canonical example. Functions are **event-driven**: valid triggers include an **EventBridge** schedule (cron-style), an S3 upload, or an API Gateway request. A subtlety the QCM tests: on an S3 trigger the function receives a **JSON document with metadata** about the event, _not_ the file's contents directly — you fetch the object yourself if needed.

The well-known performance issue is the **cold start**: when no warm instance exists, the provider must provision a new container, load the runtime, and initialize your code before execution begins, adding latency. Lambda integrates natively with **CloudWatch** for logs and metrics without extra permissions.

Serverless also has real **limitations** worth articulating. Functions can't easily maintain **state between invocations** or run long-lived tasks — durable state belongs in something like DynamoDB or a message queue. There's **vendor lock-in**, since deep integration with proprietary AWS services makes migrating elsewhere hard. And because function instances are **"nomadic"** (their underlying instances and IPs are ephemeral and keep changing), you can't rely on direct IP-to-IP communication between functions. In the "standard model" that routes work through a message broker, the main drawback is that **application state is shuttled across the network at each step**, causing sequential bottlenecks and repeated cold-start latency. A scenario that is _not_ a good serverless fit is a function that runs continuously up to its maximum timeout polling a database — that's a persistent workload, the opposite of event-driven. Lastly, **AWS Fargate** is the serverless way to launch containers (you don't manage the underlying cluster).

---

## 🤖 S6 — MLOps & Machine Learning

**MLOps** is about the _operational_ side of machine learning: reliably deploying models and managing their full lifecycle in production. It's distinct from **AutoML**, which automates the model-building side — end-to-end from data preprocessing through model selection and training. A clean way to remember the difference: MLOps standardizes the lifecycle, AutoML automates model creation.

Why is **continuous monitoring** central to MLOps? Because models degrade silently in production through **data drift** (the input distribution shifts) or **concept drift** (the relationship between inputs and target changes); monitoring catches these failures that wouldn't show up as crashes. Evaluation choices matter too: in a screening model for a treatable life-threatening disease, **false negatives** are the most dangerous outcome, since they send genuinely sick patients home untreated — far worse than a false alarm.

On the data-prep side, a Pearson correlation of **1.0** between two features signals perfect multicollinearity, and the appropriate fix is to **remove one** of them. Categorical string labels must be **encoded into numbers** before training, because standard algorithms compute gradients and optimization metrics over numeric input. **Meta-learning** ("learning to learn") systematically reuses experience from previous tasks to learn new, unseen tasks more efficiently.

In AWS terms, **SageMaker** offers two deployment modes that contrast clearly: an **Endpoint** provides _persistent_ compute for real-time inference, whereas a **Batch Transform** job provisions _ephemeral_ compute to score a bulk dataset and then tears down. And an **IAM policy** in this context defines exactly which actions a principal (user/role) can or cannot perform on which AWS resources — the backbone of access control for ML pipelines.

---

## 🧠 S7 — GenAI, MaaS & Amazon Bedrock

**Model-as-a-Service (MaaS)** is the abstraction that "democratizes" AI: rather than bearing the capital cost of training and hosting frontier models, developers consume model capability through high-level **APIs**. Conceptually it's a vertical shift in the stack — from the traditional OS-centric layering to a `Compute → Model → Interface` abstraction.

**Amazon Bedrock** is the AWS implementation: a managed service giving access to foundation models from **multiple providers through a single, unified API**. Several practical points recur. Switching models — say from Claude 3.5 Sonnet to Llama 3 70B — requires only changing the **`modelId`** parameter while keeping the same request structure. The **Converse API is stateless**, so the developer must resend **all relevant previous turns** in the `messages` array on every request; Bedrock doesn't keep session memory for you. In a **tool-calling loop**, after the model returns a `toolUse` block, your application makes **one** additional Converse call to send the tool's result back and get the final synthesized answer. On **security**, AWS's commitment is that customer input data is **never used to train base models** and stays within the customer's environment.

A few broader GenAI concepts round out the section. **Knowledge distillation** trains a compact "student" model to mimic the output distribution of a large "teacher" model, making it suitable for edge deployment. **Federated learning** preserves privacy by training locally on each node and transmitting only **weight/gradient updates** for central aggregation — raw data never leaves the device. A subtle systems point: total AI data-center power can keep **growing even as models get more efficient**, because demand elasticity (more tokens, larger models, more usage) outpaces efficiency gains. Finally, **Foundation Models** are characterised as adaptable to many tasks, pretrained on vast data, and self-supervised — so "task-specific and rigid" is _not_ a feature of them — and the **GenAI vs Predictive AI** distinction is that GenAI creates new content while Predictive AI forecasts outcomes.

---

## 🌐 SDN & Virtual Networking

> ⚠️ Answers in this section were marked "réponse déduite — à confirmer" in the source QCM, so treat them as likely-but-unverified.

The core principle of **Software-Defined Networking** is **decoupling the control plane** (the system that _decides_ how traffic flows) **from the data plane** (the hardware that actually _forwards_ packets). This separation lets you manage the network centrally through software. The **SDN controller** holds a centralized, strategic view of the whole network and translates high-level application requirements into low-level device instructions. It talks to the rest of the system through directional interfaces: the **Northbound** interface faces _upward_ to applications, offering an abstraction layer for network services to interact with higher-level logic; the **Southbound** interface faces _downward_ to the data plane, programming flow tables via protocols like OpenFlow; and East/Westbound interfaces handle controller-to-controller communication.

On the virtual networking side, a **VLAN** partitions a single physical Layer-2 infrastructure into multiple distinct broadcast domains to isolate traffic and improve security. A **virtual switch (vSwitch)** is a software entity living inside the hypervisor that forwards frames between virtual interfaces and the physical network uplinks.

---

## 🏛️ AWS Fundamentals (recurring across past exams)

Beyond the seminar themes, the past-year exams lean heavily on AWS cloud fundamentals.

**Global infrastructure.** A **Region** contains multiple isolated **Availability Zones (AZs)**, and each AZ comprises one or more discrete physical **data centers** connected by high-speed, low-latency private links. AZs are designed for fault isolation; choosing a Region close to your users reduces latency. Note that a single data center maps to exactly one AZ.

**Compute & pricing.** **EC2** offers resizable compute with full control over the OS and software stack. Its pricing models map to workloads: **On-demand** suits short, spiky, or unpredictable jobs that must not be interrupted (no commitment); **Reserved** instances cut cost but require a 1-year+ commitment; **Spot** instances are cheapest but interruptible, ideal for fault-tolerant batch processing; and **Dedicated** instances guarantee you don't share a physical host with other customers. Scaling comes in two directions — **vertical** (a bigger machine with more CPU/RAM) and **horizontal** (more instances). **Auto Scaling** adjusts the number of instances and adds value for both predictable and dynamic workloads; a launch template/configuration must specify at least the **AMI** and **instance type**. **Elastic Load Balancing (ELB)** performs health checks and distributes traffic across instances, while **CloudWatch** is what actually triggers a scaling event when a threshold is crossed.

**Resilience vocabulary.** _Fault-tolerant_ means built-in component redundancy; _highly available_ describes a system that withstands degradation with minimal downtime and minimal human intervention; _elastic/scalable_ means resources adjust to demand. The **Reliability pillar** of the Well-Architected Framework recommends replacing one large resource with many smaller ones and assuming everything fails, so you automate and test recovery.

**Networking.** A **VPC** can span AZs; its address range is fixed once created. A **main route table** is created by default, and you make a subnet public by attaching an **internet gateway** and adding a route to it. **Security groups** act as virtual firewalls controlling inbound/outbound traffic at the instance level, and **private subnets** have no direct internet access.

**Management, monitoring & security.** **CloudTrail** logs all API access (the audit trail), **Config** records and evaluates resource configuration changes, **CloudWatch** monitors metrics and performance, and **Trusted Advisor** gives best-practice recommendations (e.g. warning when MFA isn't enabled). For **IAM**, identity-based policies attach to users, groups, or roles, while resource-based policies (including ACLs) attach to resources; both deny by default. The two access types are programmatic and console access, and for temporary cross-account access the best practice is to let external users **assume a role**. **AWS Organizations** consolidates multiple accounts for central management, grouped policies, and volume discounts via consolidated billing.

**Service models & delivery.** Control decreases as you move up the stack: **IaaS** gives the most control, **PaaS** removes OS management (Elastic Beanstalk is the AWS example), and **SaaS** gives the least. A **CDN** like CloudFront caches frequently requested files at **edge locations** and regional edge caches close to users to cut latency.

**Cost & value.** Use the **Pricing Calculator** to estimate future cost, **Cost Explorer** to visualize past usage and forecast, and **Budgets** for alerts. **TCO** comparisons weigh server and storage migration costs (not the number of users or groups). The headline cloud benefits remain: trading capital expense for variable expense, benefiting from economies of scale, gaining agility, and being able to stop guessing capacity.

---