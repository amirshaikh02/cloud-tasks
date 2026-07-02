# AWS VPC Setup: Traffic Flow and Security Documentation

This repository outlines the configuration steps to construct a custom Virtual Private Cloud (VPC) on AWS with explicit subnets, routing rules, internet gateways, and layered firewall controls (Security Groups and Network Access Control Lists).

---

## Technical Specifications Matrix

| Resource Type | Resource Name | Key Parameters / Settings |
| :--- | :--- | :--- |
| **VPC** | `cloud-vpc` | IPv4 CIDR: `10.0.0.0/16` \| Tenancy: Default |
| **Subnet** | `subnet1` | IPv4 CIDR: `10.0.0.0/24` \| Auto-Assign Public IP: Enabled |
| **Internet Gateway** | `cloud-igw` | Attached to `cloud-vpc` |
| **Route Table** | Custom RT | `0.0.0.0/0` &rarr; `cloud-igw` \| `10.0.0.0/16` &rarr; `local` |
| **Security Group** | `cloud-sg` | Inbound: HTTP (Port 80) from Anywhere (`0.0.0.0/0`) |
| **Network ACL** | `cloud-NACL` | Rule 100: Allow All Traffic from Anywhere (`0.0.0.0/0`) |

---

## Configuration Architecture Steps

### Step 1: Establish the Virtual Private Cloud (VPC)
- **Settings**: Choose **VPC only**.
- **Name assignment**: `cloud-vpc`
- **Network Block Allocation**: Set manual input IPv4 CIDR to `10.0.0.0/16`.

![Create VPC](images/VPC1.png)

---

### Step 2: Provision the Public Subnet & Enable Public Routing
- Select **Subnets** from the left navigation and click **Create subnet**.
- Link to VPC ID corresponding to `cloud-vpc`.
- Set Subnet Name to `subnet1` and designate an explicit Availability Zone (e.g., `ap-south-1a`).
- Define the IPv4 Subnet CIDR block as `10.0.0.0/24`.

*Post-Creation Action*: Select the created subnet &rarr; **Actions** &rarr; **Edit subnet settings** &rarr; Check **Enable auto-assign public IPv4 address**.

![Subnet Creation Panel](./images/page_2_img_1.png)
![Edit Subnet Settings Window](./images/page_2_img_3.png)

---

### Step 3: Configure and Attach the Internet Gateway (IGW)
- Navigate to **Internet gateways** and click **Create internet gateway**.
- Name tag the resource as `cloud-igw`.
- Once generated, choose **Actions** &rarr; **Attach to VPC** and associate it directly with your newly provisioned `cloud-vpc`.

![Create Internet Gateway](./images/page_3_img_1.png)
![Attach IGW Interface](./images/page_3_img_3.png)

---

### Step 4: Map Custom Routes for Internet Transit
- Open **Route tables**. A route table serves as the network's directional guide.
- Select the route table linked with your custom VPC and click **Edit routes**.
- Define an outbound path mapping to destination `0.0.0.0/0` via target **Internet Gateway** (`cloud-igw`).
- Ensure the internal network tracking `10.0.0.0/16` remains mapped to `local`.

![Route Table Overview](./images/page_4_img_2.png)
![Edit Custom Routes Panel](./images/page_5_img_1.png)

---

### Step 5: Associate Subnets explicitly
- Within the route table details dashboard, navigate to the **Subnet associations** tab.
- Click **Edit subnet associations**, check the box next to `subnet1`, and click **Save associations**.

![Subnet Associations Subtab](./images/page_5_img_2.png)
![Confirm Subnet Associations](./images/page_6_img_1.png)

---

### Step 6: Create Stateful Security Group Policies
- Navigate to **Security groups** under the Security menu.
- Click **Create security group**:
  - **Name**: `cloud-sg`
  - **Description**: `A Security Group for the cloud-vpc`
  - **VPC**: Select `cloud-vpc`
- Add an **Inbound rule** allowing **HTTP** traffic (Port 80) from source **Anywhere-IPv4** (`0.0.0.0/0`).

![Security Group Settings](./images/page_6_img_2.png)

---

### Step 7: Formulate Stateless Network ACL (NACL) Rules
- Select **Network ACLs** and select **Create network ACL** matching name `cloud-NACL` for `cloud-vpc`.
- Network ACLs act as rigorous boundary checkpoint guardians at the subnet border.
- Click **Edit inbound rules** and add the following condition:
  - **Rule Number**: `100`
  - **Type**: `All traffic`
  - **Source**: `0.0.0.0/0`
  - **Action**: `Allow`
- Explicitly connect this NACL via the **Subnet associations** tab to `subnet1`.

![Network ACL Workspace](./images/page_7_img_1.png)
![Inbound Rule Matrix Specification](./images/page_8_img_1.png)
