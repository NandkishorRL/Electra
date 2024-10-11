# Launching an EC2 Instance:

> Give name, select AMI or Operating System, KeyPair (login), Instance Type.

> In Network Settings, select the VPC in which the server should reside, Subnet, and Create a New Security Group allowing SSH Access from a particular IP and after that launch the instance. 

> Once the instance comes into the running state, open the command prompt - change directory to the folder where we have downloaded the key pem file and run the command `chmod 400 key.pem`, then run the command `ssh -i "key.pem username@PublicIP"` & we will get connected to the server. After that we can run the normal commands. 

> Another way we can connect to the instance is - Go to the EC2 Console and select the checkbox beside the instance - click on connect and then in EC2 instance connect option scroll down and click on connect, then we will get connected to the EC2 Instance. 


# How to change the instance type of a instance:

- For Example, we want to change the instance type of the instance from `t2.micro` to `t2.small` because we want more RAM or CPU, So this only works for EBS Backed Instances. 

> Connect to the instance and create a `hello.txt` file using `echo hello > hello.txt` and then use the `free -m` command to view the memory space available. After that click on the console and stop the instance - then select the instance, right click and go to insatnce settings and click on change the instance type. From there we can select any instance type in which we want to upgrade or downgrade. 

> Some instance types that we can see have EBS-Optimized enabled by default, EBS-Optimized means we are going to have a better throughput for our EBS Volumes and this is available for new generation instance types for e.g -> "t3". After selecting the required instance type click on apply to apply the changes. 

> Now our instance type will change to `t2.small`, then again right click on the instance and click on start instance to start the server. what's going to happen when we start & stop the EC2 Instances is it's going to be restarted on another physical host within AWS Data Center, that means the instance will change it's hardware. But because the storage is EBS based and `/dev/xvda` is a block device as an EBS Volume, then the data will remain the same. 

> Again connect to the instance, look for the data if it is present, again run the command `free -m` to check the memory space available - then we can see that the RAM is increased as compared to previous instance because we have upgraded the instance type.


# What is an AMI?

- An AMI contains the software configuration (for example, an operating system, an application server, and applications) required for the creation of EC2 instances. When we launch an instance, we must specify an AMI. 

* AMIs are categorized by the storage for the root device of an instance launched from the AMI, as follows:

1. EBS-backed AMI: The instance’s root device is an EBS volume created from an EBS snapshot.

2. Instance store-backed AMI: The instance’s root device is an instance store volume created from a template stored in S3.


* Launch instances: We can launch multiple instances from a single AMI when we need multiple instances with the same configuration. When we need instances with different configurations, we can launch each instance from a different AMI. 

* Copy AMI: We can copy an AMI within a Region, or across Regions within the same partition. Edit AMI permissions:- We can determine who can use our AMI by sharing our AMI with all or specific AWS accounts. Manage tags:- We can categorize our AMIs by assigning our own custom labels in the form of tags. Deregister AMI - We can deregister an AMI so that it can't be used to launch new instances. Deregistering an AMI does not affect any instances that we've already launched from the AMI. Change description:- We can edit the description of our AMI.

* Register instance store-backed AMI: After customizing & bundling our instance-store backed AMI and uploading the bundle to an Amazon S3 bucket, we must register it before we can launch an instance from it. Costs for EBS-backed AMIs:- We're charged for EBS volume storage & usage, and storing our AMI as an EBS snapshot. Each time we customize an AMI and create a new one, only the changes are stored. So, the storage footprint for subsequent AMIs that you customize after the first is much smaller, resulting in lower AMI storage charges.

* Costs for EBS-backed AMIs: We're charged for EBS volume storage and usage, and storing our AMI as an EBS snapshot. Each time we customize an AMI and create a new one, only the changes are stored. So, the storage footprint for subsequent AMIs that you customize after the first is much smaller, resulting in lower AMI storage charges.

* Costs for instance store-backed AMIs: We're charged for storing our AMI in Amazon S3. Each time we customize an AMI and create a new one, all of the parts are stored in Amazon S3 for each AMI. So, the storage footprint for each customized AMI is the full size of the AMI.


# What is the Difference Between EBS-Backed and Instance Store Backed:

* The main difference between EBS-backed and Instance Store-backed Amazon EC2 instances lies in the way their root volumes are managed and persisted. Here's a breakdown of the differences:

1. EBS-Backed Instances:

- Root Device Storage: Uses Amazon Elastic Block Store for root volume storage.
- Persistence: Data on the root volume is persistent across instance stops, reboots, or terminations (if configured to keep the volume).
- Snapshot Support: EBS volumes can be snapshotted and backed up to Amazon S3, allowing easy recovery and replication.
- Instance Start/Stop: We can stop and start an EBS-backed instance. When stopped, the data on the EBS volume remains intact.
- Performance: EBS provides flexible and scalable options with provisioned IOPS, depending on the type of volume.
- Resizing: We can resize the EBS volume or change its type even after the instance is running.
- Cost: We pay for both the EC2 instance and the EBS storage (even when the instance is stopped).


2. Instance Store-Backed Instances:

- Root Device Storage: Uses Instance Store (ephemeral storage) for the root device.
- Persistence: Data stored in instance store volumes is not persistent. It is lost when the instance is stopped, rebooted, or terminated.
- Snapshot Support: Instance Store-backed instances do not support snapshots. Data cannot be backed up or restored automatically.
- Instance Start/Stop: We cannot stop and start an Instance Store-backed instance. We can only terminate and relaunch it, resulting in data loss on the root device.
- Performance: Instance Store-backed instances often offer very high I/O performance, but this is specific to the hardware of the host.
- Resizing: We cannot resize instance store volumes, as they are tied to the instance’s physical hardware.
- Cost: Typically less expensive than EBS-backed instances because the storage is part of the instance and doesn't incur additional storage costs.

* Summary: 

- EBS-Backed: Persistent storage, supports snapshots, can be stopped/started, scalable and flexible.
- Instance Store-Backed: Ephemeral (non-persistent) storage, no snapshot support, data is lost on stop/termination, more suited for temporary or stateless workloads.


# What is a Key Pair:

- A Key Pair in the context of an Amazon EC2 instance refers to a set of cryptographic keys used for securely accessing and managing the instance. It consists of two parts: a public key and a private key.

* Key Pair Overview in EC2:

1. Public Key: AWS stores this key on the EC2 instance. It is used to encrypt login credentials when we try to access the instance.

2. Private Key: We download this key (as a `.pem` file) when the key pair is created. It is used to decrypt the login credentials (e.g., for SSH login to Linux instances or for RDP access to Windows instances).


* What Happens When we Create a New Key Pair:

- Private Key Creation: AWS generates a private key for us, which we must download immediately. AWS does not retain this key after generation.

- Public Key Upload: AWS stores the corresponding public key on the EC2 instance when it is launched.

- Instance Access: When we attempt to connect to our EC2 instance via SSH (Linux) or RDP (Windows), the public and private keys are used for authentication. The private key decrypts the credentials encrypted by the public key. It’s important to securely store the private key file (.pem), as it is the only way to access our instance. If we lose it, we won’t be able to connect to the instance unless we configure an alternative access method.

* Difference Between RSA and ED25519 Key Pairs:

1. RSA (Rivest–Shamir–Adleman):

- Cryptographic Strength: RSA keys have been used for decades and are generally secure. However, to maintain security in the modern world, RSA keys typically need to be 2048 or 4096 bits long.

- Speed: RSA is slower, both for key generation and for authentication, especially with larger key sizes.

- Compatibility: RSA is widely supported across almost all systems and platforms, making it the more universally compatible choice.

- Security: RSA has been tested over time and remains secure with longer key sizes, though larger keys are required to ensure security in the face of advancing computing power.


2. ED25519 (Edwards-curve Digital Signature Algorithm):

- Cryptographic Strength: ED25519 is based on elliptic curve cryptography and provides comparable security to RSA-3072 with a much shorter key (256 bits).

- Speed: ED25519 is significantly faster in both key generation and signing/verification operations compared to RSA, making it a more efficient algorithm.

- Size: ED25519 keys are smaller, typically 256 bits, yet offer high levels of security. This smaller size translates to faster performance and reduced bandwidth.

- Compatibility: ED25519 is newer and may not be supported by older software and platforms. However, modern systems widely support it, especially for SSH.

* Summary of RSA vs. ED25519:

1. RSA: Older, larger keys (2048 or 4096 bits), slower, but universally compatible.

2. ED25519: Newer, faster, more secure with smaller keys (256 bits), but slightly less compatible with legacy systems. In general, ED25519 is recommended for its performance and strong security, unless we need compatibility with older systems, where RSA may be the better choice.


# Types of Instances:

1. General Purpose: General purpose instances provide a balance of compute, memory and networking resources, and can be used for a variety of diverse workloads. These instances are ideal for applications that use these resources in equal proportions such as web servers and code repositories. 

* Use cases: Applications built on open source software such as application servers, microservices, gaming servers, midsize data stores, and caching fleets. 


2. Compute Optimized: Compute Optimized instances are ideal for compute bound applications that benefit from high performance processors. Instances belonging to this category are well suited for batch processing workloads, media transcoding, high performance web servers, high performance computing (HPC), scientific modeling, dedicated gaming servers and ad server engines, machine learning inference and other compute intensive applications.

* Use Cases: High performance computing (HPC), batch processing, ad serving, video encoding, gaming, scientific modeling, distributed analytics, and CPU-based ML inference.


3. Memory Optimized: Memory optimized instances are designed to deliver fast performance for workloads that process large data sets in memory. Mostly used in Memory-intensive workloads such as open source databases, in-memory caches, and real-time big data analytics.


4. Accelerated Computing: Accelerated computing instances use hardware accelerators, or co-processors, to perform functions, such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs. Mostly used in Generative AI applications, including question answering, code generation, video and image generation, speech recognition, and more. HPC applications at scale in pharmaceutical discovery, seismic analysis, weather forecasting, and financial modeling.


5. Storage Optimized: Storage optimized instances are designed for workloads that require high, sequential read and write access to very large data sets on local storage. They are optimized to deliver tens of thousands of low-latency, random I/O operations per second (IOPS) to applications. Amazon EC2 I4g instances are optimized for I/O intensive applications and are targeted to customers using transactional databases (Amazon DynamoDB, MySQL, and PostgreSQL), Amazon OpenSearch Service, and real-time analytics such as Apache Spark. 


6. HPC Optimized: High performance computing (HPC) instances are purpose built to offer the best price performance for running HPC workloads at scale on AWS. HPC instances are ideal for applications that benefit from high-performance processors such as large, complex simulations and deep learning workloads. It has 500 Mbps network bandwidth outside the virtual private cloud (VPC) and Amazon Simple Storage Service (Amazon S3).


* EBS-optimized Instances: For an additional, low, hourly fee, customers can launch selected Amazon EC2 instances types as EBS-optimized instances. EBS-optimized instances enable EC2 instances to fully use the IOPS provisioned on an EBS volume. EBS-optimized instances deliver dedicated throughput between Amazon EC2 and Amazon EBS, with options between 500 Megabits per second (Mbps) and 80 Gigabits per second (Gbps), depending on the instance type used. The dedicated throughput minimizes contention between Amazon EBS I/O and other traffic from our EC2 instance, providing the best performance for our EBS volumes. EBS-optimized instances are designed for use with all EBS volumes. When attached to EBS-optimized instances, Provisioned IOPS volumes can achieve single digit millisecond latencies and are designed to deliver within 10% of the provisioned IOPS performance 99.9% of the time. We recommend using Provisioned IOPS volumes with EBS-optimized instances or instances that support cluster networking for applications with high storage I/O requirements. 


* Hi Guys, how are you.