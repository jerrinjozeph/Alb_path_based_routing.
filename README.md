## AWS Application Load Balancer: Implementation of Path-Based Routing
Amazon’s Application Load Balancer (ALB) is a highly scalable and efficient solution for distributing incoming application traffic across multiple targets. By enabling path-based routing, ALB allows you to host multiple applications on a single load balancer, making it a versatile choice for managing complex application infrastructures. In this blog, we will explore how to configure ALB with path-based routing, 

--------------------------------------------------------------------------------

### Benefits of Using Path-Based Routing with ALB
- Simplified Architecture: By hosting multiple applications on a single ALB, you can reduce the number of load balancers and network configurations, simplifying your infrastructure.
- Resource Optimization: ALB’s dynamic path-based routing efficiently distributes traffic based on request paths, ensuring optimal utilization of resources.
- Enhanced Flexibility: Path-based routing enables easy addition, removal, or update of application paths, allowing you to adapt to changing requirements without disruptions.
- Improved Security: Secure Sockets Layer (SSL) termination and support for multiple SNI and domains in ALB enhance the security of your applications and customer data.

--------------------------------------------------------------------------------

### 1. Backend Infrastructure: EC2 Instances
The application is powered by four separate t3.micro EC2 instances running in the ap-south-2a availability zone. These instances represent the different functional areas of the shopping application:
- main-server: The landing page.
- orders_instance: Handles order processing.
- billing_instance: Manages payment and billing.
- items_instance: Serves the product catalog.
<img width="1884" height="310" alt="instances_ss" src="https://github.com/user-attachments/assets/19300a5f-7b14-404c-8ef5-5e493d34cedd" />

--------------------------------------------------------------------------------
### 2. Target Group Configuration
Each EC2 instance is associated with a specific Target Group. All target groups are configured to receive traffic via HTTP on Port 80.
- main-server-tg
- orders-tg
- billing-tg
- items-tg
 
<img width="1521" height="648" alt="all_tgs" src="https://github.com/user-attachments/assets/35d46ae8-8a06-4ee3-833f-ad9594603a35" />

--------------------------------------------------------------------------------
### 3. Application Load Balancer (ALB) Setup
The load balancer, named shopping-lb, is an internet-facing ALB deployed within a specific VPC. It features two primary listeners:
- HTTP:80: Automatically redirects all traffic to HTTPS:443 (301 Permanent Redirect) to ensure secure connections.
- HTTPS:443: The primary secure listener that evaluates routing rules.
<img width="1882" height="715" alt="alb" src="https://github.com/user-attachments/assets/98b47fc5-cf9d-4b95-8572-11d795127720" />

--------------------------------------------------------------------------------

### 4. Path-Based Routing Rules
The core logic resides in the HTTPS listener rules, which route traffic to specific target groups based on the requested path:
<img width="1654" height="692" alt="alb_rules" src="https://github.com/user-attachments/assets/474a3d8e-95a3-4a4c-a452-de3d064cdb22" />


--------------------------------------------------------------------------------
### 5. DNS Integration with Route 53
To provide a user-friendly URL, a Route 53 Alias record was created for the domain shopping.hazeljuan.online. This record points directly to the DNS name of the shopping-lb load balancer.
<img width="1637" height="43" alt="r53" src="https://github.com/user-attachments/assets/3d587616-2f63-4d0d-989e-c3393f2f22e6" />


--------------------------------------------------------------------------------
6. Application Verification
When accessing https://shopping.hazeljuan.online, the ALB correctly routes the user to the main-server-tg, displaying the Shopping Application Main page. From here, users can navigate to the specialized sections (Billing, Items, Orders), and the ALB will handle the routing seamlessly based on the defined path rules.

<img width="1535" height="648" alt="alb_check" src="https://github.com/user-attachments/assets/5d5b2af3-4a8f-4e34-afbb-0052c256ac6a" />
