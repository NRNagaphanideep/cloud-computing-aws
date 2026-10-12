Day 56: Production Lab - Multi-Tier HA Application & AWS Load Balancing

## 1. Detailed Topic Explanations

### A. Multi-Tier HA (High Availability) Application
* **Web, App, and Data Tiers Across Multiple AZs**: Designing an architecture that isolates different components of an application into dedicated web tiers, application tiers, and database tiers, while distributing them across multiple Availability Zones (AZs) to ensure fault tolerance and uninterrupted service availability[cite: 4].
* **Public/Private Network Segmentation**: Enhancing security by separating network segments into public subnets (hosting internet-facing elements like web servers) and private subnets (hosting backend application logic and databases)[cite: 4].
* **Integrated Infrastructure Controls**: Managing traffic distribution with load balancers, protecting workloads using security controls, resolving domains via DNS, and observing system performance through monitoring tools[cite: 4].
* **Build Architecture and Deploy**: Assembling these core infrastructure blocks from scratch to build a resilient, production-grade architecture and successfully deploying the application[cite: 4].

### B. AWS Load Balancing
* **Application Load Balancer (ALB)**: Operating at Layer 7 (the application layer) to intelligently route HTTP and HTTPS web traffic[cite: 4].
* **Network Load Balancer (NLB)**: Operating at Layer 4 (the transport layer) to manage TCP, UDP, and ultra-high-performance workloads requiring minimal latency[cite: 4].
* **Core Components**: 
  * **Listeners**: Checking for incoming connection requests based on configured rules and ports[cite: 4].
  * **Target Groups**: Routing requests to registered targets (such as EC2 instances)[cite: 4].
  * **Health Checks**: Continuously monitoring the status of backend targets to ensure traffic is only routed to healthy nodes[cite: 4].
* **Practical Configuration & Use Cases**: Hands-on setup of an ALB while clearly understanding the architectural distinctions and specific scenarios where an NLB should be selected[cite: 4].

---

## 2. Hands-On Practice Guide

### Lab Scenario: Deploying a Multi-Tier Architecture with an Application Load Balancer (ALB)

#### Step 1: Design and Build the Network (VPC Setup)
1. **Create a VPC**: Set up a custom VPC with a CIDR block (e.g., `10.0.0.0/16`).
2. **Configure Subnets**:
   * **Public Subnets**: Create two public subnets across two Availability Zones (AZ-1 and AZ-2) for the web/load balancer tier[cite: 4].
   * **Private Subnets**: Create two private subnets across the same AZs for the database or backend application tier[cite: 4].
3. **Internet Gateway & Route Tables**: Attach an Internet Gateway (IGW) to the VPC and configure a public route table for the public subnets to allow outbound internet access.

#### Step 2: Configure an Application Load Balancer (ALB)
1. Navigate to the **EC2 Console** > **Load Balancers** > Click **Create Load Balancer**.
2. Select **Application Load Balancer (ALB)** and click **Create**[cite: 4].
3. **Basic Configuration**:
   * Name your ALB (e.g., `prod-web-alb`).
   * Select **Internet-facing** scheme.
   * Choose your custom VPC and select both public subnets across different AZs.
4. **Security Groups**: Create or assign a security group allowing inbound HTTP (`Port 80`) and HTTPS (`Port 443`) from anywhere (`0.0.0.0/0`).
5. **Listeners and Routing**:
   * Configure a listener on **HTTP:80**[cite: 4].
   * Click **Create target group**, select **Instances**, name it (e.g., `web-target-group`), and set the protocol to HTTP[cite: 4].
   * Configure **Health checks** (path: `/index.html`, interval: 30 seconds) to verify instance responsiveness[cite: 4].
6. Complete the wizard and create the ALB.

#### Step 3: Deploy Web Tier Instances
1. Launch two EC2 web instances, placing one in Public Subnet AZ-1 and the other in Public Subnet AZ-2[cite: 4].
2. Install a basic web server (e.g., Nginx or Apache) on both instances via user data or Session Manager.
3. Register these two instances under your newly created `web-target-group`.
4. Test the architecture by copying the ALB DNS name into a browser; traffic should balance across both instances seamlessly.

