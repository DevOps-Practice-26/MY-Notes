# **Infrastructure as Code (IaC) – Detailed Explanation with Real-Time Examples**

You have covered the important advantages of Infrastructure as Code (IaC). Your understanding is correct. Let's understand each point in detail using **Terraform and AWS examples**, and then I'll give you interview-ready answers.

## **1. What is Infrastructure as Code (IaC)?**

Infrastructure as Code means **creating, managing, configuring, and deleting infrastructure using code instead of manually creating resources through a cloud console.**

For example, imagine your organization wants to create the following AWS infrastructure:

- 1 VPC
- 2 Public subnets
- 2 Private subnets
- 1 Application Load Balancer
- 2 EC2 instances
- 1 RDS database
- Security groups

There are two ways to create this infrastructure.

**Approach 1: Manual infrastructure creation**

[Introduction to AWS Networking - Earthly Blog](https://images.openai.com/static-rsc-4/UpUnnqc05lAGL_FRoFRWhXxQ6zsStpF48StWpcwcBCFvHph3NXsx0TkOZ9fuRursGlV600OicqbOV2GlYlPmsenx06P7-2GPNLIlcDm0FA5TRWLGC434WIaC8_o4F0oJ9AHKcNvkZDBC08MgR9H8ZR19myfIeVYGbb8MIy9DMbc?purpose=inline)

[earthly.dev](https://earthly.dev/blog/aws-networks/?utm_source=chatgpt.com)

1. Login to AWS Console.
2. Navigate to VPC.
3. Create VPC and subnets.
4. Navigate to EC2 and launch instances.
5. Configure security groups.
6. Create the load balancer and RDS database.

Time-consuming and error-prone

**Approach 2: Infrastructure as Code**

[Complete Guide: Infrastructure as Code with Terraform on AWS — part 1 | by Reda Abdou | Medium](https://images.openai.com/static-rsc-4/X5B4T9-BwSCdt13Y2Y6qhmobFepwyceVRU6YknR8P50wX-UbvNC3nqv59y8_vG20Tk58a2qeqSwROTKE8eawSBBSgM2N3dUZs5oSnBGMnfkCCOsZrbaXoU5q-OLhre57gvNNCTwrc4WMw8-Y50uQvgH_qLKkBKPQvLaoEswIcrs?purpose=inline)

[medium.com](https://medium.com/%40redaabdou/complete-guide-infrastructure-as-code-with-terraform-on-aws-part-1-338f7bfd1933?utm_source=chatgpt.com)

1. Write Terraform code.
2. Run `terraform init`.
3. Run `terraform plan`.
4. Run `terraform apply`.

Repeatable and automated

**Real-time example:** Suppose you have to create the same infrastructure in DEV, SIT, UAT and PROD. Instead of manually creating resources four times, you can reuse Terraform code with different environment configurations.

## **2. Advantages of Infrastructure as Code**

Let's understand your six points one by one.

### **1. Version Control**

**Meaning:** We can maintain infrastructure code in version control systems such as GitHub, GitLab or Bitbucket.

Why is this useful?

Imagine you created an EC2 instance using Terraform.

Initially, you configured:

```
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t3.micro"
}
```

After a few days, the application team requests a bigger EC2 instance.

You change the configuration:

```
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t3.medium"
}
```

Now you commit the change to GitHub.

```
git add .
git commit -m "Updated EC2 instance type"
git push origin main
```

**What are the benefits?**

- We can track who changed the infrastructure.
- We can check what changes were made.
- We can review code before deploying infrastructure.
- We can collaborate with other team members.
- We can maintain the history of infrastructure changes.

**Interview example:**

Suppose someone accidentally changes the production EC2 instance type. Using Git history, we can identify the previous configuration and restore the code.

Remember: Restoring the Git code alone does not restore the actual infrastructure. We must run Terraform and review the proposed changes.

### **2. CRUD Operations**

CRUD stands for:

| Operation  | Meaning                         | Terraform example               |
| ---------- | ------------------------------- | ------------------------------- |
| C – Create | Create infrastructure           | `terraform apply`               |
| R – Read   | Read infrastructure information | `terraform show`                |
| U – Update | Modify infrastructure           | Change code + `terraform apply` |
| D – Delete | Delete infrastructure           | `terraform destroy`             |

Let's understand with an EC2 example.

**Create:**

```
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t3.micro"
}
```

Run:

```
terraform apply
```

This creates an EC2 instance.

**Read:**

```
terraform show
```

This displays the infrastructure tracked in Terraform state.

**Update:**

Change:

```
instance_type = "t3.medium"
```

Run:

```
terraform apply
```

Terraform evaluates the difference and updates the infrastructure. Depending on the attribute, it may modify the existing resource or replace it.

**Delete:**

```
terraform destroy
```

This deletes the resources managed by that Terraform configuration.

Production caution

Always review `terraform plan` before applying changes or destroying resources, particularly in production.

### **3. Consistent Infrastructure**

**Meaning:** IaC helps us create similar infrastructure across multiple environments.

Let's consider a real-time company scenario.

Your organization has four environments:

**DEV**

# **t3.micro**

Development testing

**SIT**

# **t3.small**

Integration testing

**UAT**

# **t3.medium**

User acceptance testing

**PROD**

# **t3.large**

Production workloads

Illustrative instance sizes, not a recommended production sizing plan.

Suppose the DEV team creates an application and it works perfectly.

When the application moves to production, it fails because the production infrastructure was configured differently.

This is a common problem called environment inconsistency.

**How does Terraform solve this?**

We can maintain common Terraform code and pass different variables for each environment.

For example:

`variables.tf`

```
variable "instance_type" {
  type = string
}
```

`main.tf`

```
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = var.instance_type
}
```

`dev.tfvars`

```
instance_type = "t3.micro"
```

`prod.tfvars`

```
instance_type = "t3.large"
```

Deploy DEV:

```
terraform apply -var-file="dev.tfvars"
```

Deploy PROD:

```
terraform apply -var-file="prod.tfvars"
```

Now the same Terraform logic can create infrastructure in different environments with different configurations.

**Important interview point:** Consistency does not mean every environment must have identical resources or sizes. It means we follow the same infrastructure design and standards while allowing environment-specific configurations.

### **4. Inventory Management**

**Meaning:** IaC helps us understand and maintain a record of the infrastructure resources that are defined and managed through code.

Imagine you join a new organization as a DevOps engineer.

Your manager asks:

- How many EC2 instances are running?
- How many VPCs are configured?
- Which RDS databases are being used?
- Which S3 buckets are part of the application?
- Which security groups are associated with the application?

If the organization has properly maintained Terraform code, you can examine the Terraform configuration to understand the intended infrastructure.

For example:

```
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t3.micro"
}

resource "aws_s3_bucket" "app" {
  bucket = "my-company-app-bucket"
}

resource "aws_db_instance" "database" {
  allocated_storage = 20
  engine            = "mysql"
  instance_class    = "db.t3.micro"
}
```

From this code, you can identify:

| Resource          | AWS service |
| ----------------- | ----------- |
| `aws_vpc`         | VPC         |
| `aws_instance`    | EC2         |
| `aws_s3_bucket`   | S3          |
| `aws_db_instance` | RDS         |

**How does this help in real-time projects?**

Suppose your organization has 100 AWS resources. Instead of manually checking every resource in the AWS Console, you can inspect Terraform code and state to understand which resources Terraform manages.

Useful commands:

```
terraform state list
```

Lists resources tracked in the Terraform state.

```
terraform state show aws_instance.web
```

Shows details of a particular resource in Terraform state.

**Important interview point:** Terraform code and state are not necessarily a complete inventory of your AWS account. Resources created manually or managed by other tools might not appear in them. For a complete cloud inventory, AWS Config or AWS Resource Explorer can also be used.

### **5. Cost Optimization**

This is one of the important advantages of IaC in real-time organizations.

**Meaning:** We can automate infrastructure provisioning and decommissioning to reduce unnecessary cloud costs.

Let's take an example.

Your organization has a DEV environment containing:

- 2 EC2 instances
- 1 RDS database
- 1 Load Balancer

The developers work from 8 AM to 8 PM.

After 8 PM, nobody uses the DEV environment, but the infrastructure continues running.

This results in unnecessary costs.

**How can we optimize it?**

We can schedule resources to stop after business hours and start again before business hours.

**DEV environment schedule**

Start

# **8:00 AM**

Infrastructure ON

Stop

# **8:00 PM**

Infrastructure OFF

Illustrative daily schedule, Monday–Friday.

**Potential savings**

Reduced running hours

Instead of running EC2 instances for 24 hours, they run for approximately 12 hours per working day.

**How can we implement this in AWS?**

There are several approaches:

1. AWS Instance Scheduler – schedule EC2 and supported resources.
2. AWS Lambda + EventBridge Scheduler – execute scheduled start/stop scripts.
3. Terraform – provision the infrastructure and scheduling components.

For example, an EventBridge Scheduler can trigger a Lambda function at 8 PM to stop DEV EC2 instances and another at 8 AM to start them.

**Important interview point:**

Terraform itself does not automatically stop and start EC2 instances based on business hours just because we write Terraform code.

We use scheduling services such as EventBridge Scheduler and Lambda to implement this automation.

Also, stopping EC2 does not necessarily stop all associated costs. EBS volumes, Elastic IPs in certain configurations, load balancers and other resources may continue incurring charges. RDS requires its own scheduling considerations.

### **6. Dependency Management**

This is another very important Terraform concept.

**Meaning:** IaC allows us to define relationships and dependencies between infrastructure resources so that they are created and destroyed in the correct order.

Let's understand with a real-time AWS example.

Suppose you want to create:

1. VPC
2. Subnet
3. EC2 instance

Can you create an EC2 instance in a subnet before the subnet exists?

No. The subnet must exist before the EC2 instance can be created in it.

**Infrastructure dependency**

**VPC**

First resource

Depends on VPC

**Subnet**

Second resource

Depends on subnet

**EC2 instance**

Third resource

**How does Terraform understand this dependency?**

Example:

```
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"
}

resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t3.micro"
  subnet_id     = aws_subnet.public.id
}
```

Observe these lines:

```
vpc_id = aws_vpc.main.id
```

And:

```
subnet_id = aws_subnet.public.id
```

Terraform understands the dependency automatically through these resource references.

This is called **Implicit Dependency**.

There is another type called **Explicit Dependency**.

Example:

```
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t3.micro"

  depends_on = [
    aws_subnet.public
  ]
}
```

Here, we explicitly tell Terraform that the EC2 instance must wait for the subnet.

**Interview question: What is the difference between implicit and explicit dependency?**

- Implicit dependency: Terraform automatically identifies the relationship through resource references.
- Explicit dependency: We manually specify the dependency using `depends_on`.

**One more important point:** Terraform generally creates resources in dependency order and destroys them in reverse dependency order.

### **7. Modules – Reusability and Extensibility**

You mentioned modules as an advantage. This is one of the most important Terraform interview topics.

**Meaning:** A Terraform module is a collection of Terraform configuration files grouped together to create reusable infrastructure.

Let's consider a real-time scenario.

Your organization has 10 applications.

Every application needs:

- VPC
- Public subnet
- Private subnet
- Internet Gateway
- Route tables

If you write the entire VPC configuration separately for all 10 applications, you will have duplicate code.

Instead, create one reusable Terraform module.

**Reusable VPC module**

**terraform-aws-vpc**

Reusable module

**DEV**

**SIT**

**PROD**

One module reused with different inputs for multiple environments.

**Example folder structure:**

```
terraform-project/
│
├── modules/
│   └── vpc/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
│
├── dev/
│   ├── main.tf
│   └── terraform.tfvars
│
└── prod/
    ├── main.tf
    └── terraform.tfvars
```

Inside the VPC module, we write the actual VPC resources.

`modules/vpc/main.tf`

```
resource "aws_vpc" "main" {
  cidr_block = var.cidr_block

  tags = {
    Name = var.vpc_name
  }
}
```

`modules/vpc/variables.tf`

```
variable "cidr_block" {
  type = string
}

variable "vpc_name" {
  type = string
}
```

Now, in DEV, we call the module:

`dev/main.tf`

```
module "vpc" {
  source     = "../modules/vpc"
  cidr_block = "10.0.0.0/16"
  vpc_name   = "dev-vpc"
}
```

In PROD:

`prod/main.tf`

```
module "vpc" {
  source     = "../modules/vpc"
  cidr_block = "10.1.0.0/16"
  vpc_name   = "prod-vpc"
}
```

Now we reuse the same VPC module for both environments.

**Benefits of modules:**

- Code reusability.
- Reduced code duplication.
- Easy maintenance.
- Standardized infrastructure.
- Easier collaboration between teams.

**Real-time example:** A central DevOps team creates approved VPC, EKS and RDS modules. Application teams reuse these modules instead of creating their own infrastructure from scratch.

You have also worked with the `terraform-aws-vpc` module concept before. This is exactly the same idea: writing reusable infrastructure code and calling it from another Terraform project.

## **3. How all these advantages work together in a real-time DevOps project**

Imagine you join a company as an AWS DevOps Engineer.

Your company wants to deploy a Java application on AWS.

The infrastructure requirement is:

[AWS Three-Tier Web App Architecture Diagram | Diagrams.so](https://images.openai.com/static-rsc-4/6a6AkS_oEO6T4e5ATp029fg_FpxeeW_Zf3w0fFgHg10UvYfO976Gsxipeum3kg8mJr_n5eCloHBpHmu9jYYXN9XdUbjC7V_yNQ7ipPS4CKRzvaukrnxzJ3k1GLHl65pJ2Z8HwrCoINc-xWGcE4rZ3YL_uJCUoxhQqyJz92bhZw8?purpose=inline)

[diagrams.so](https://diagrams.so/d/aws-three-tier-web-application-architecture-It7x3A?utm_source=chatgpt.com)

| Component   | Infrastructure                |
| ----------- | ----------------------------- |
| Networking  | VPC, subnets, route tables    |
| Frontend    | Application Load Balancer     |
| Application | EC2 instances                 |
| Database    | RDS MySQL                     |
| Storage     | S3 bucket                     |
| Security    | Security groups and IAM roles |

How will you use IaC?

1. **Write Terraform code**

   Define all AWS resources using Terraform configuration files.
2. **Maintain code in GitHub**

   Developers and DevOps engineers commit their infrastructure changes to GitHub.
3. **Create reusable modules**

   Create VPC, EC2, RDS and security group modules.
4. **Deploy across environments**

   Reuse the Terraform code for DEV, SIT, UAT and PROD with different variable values.
5. **Manage dependencies**

   Terraform identifies which resources must be created first.
6. **Optimize costs**

   Schedule non-production resources to stop outside business hours.
7. **Maintain infrastructure inventory**

   Use Terraform state and AWS inventory services to track resources.

## **4. Interview-ready answer**

If the interviewer asks:

**"What are the advantages of Infrastructure as Code?"**

You can answer like this:

"Infrastructure as Code provides several advantages in cloud infrastructure management.

1\. **Version Control:** We maintain infrastructure code in GitHub or GitLab, which helps us track changes, maintain history and collaborate with team members.

2\. **CRUD Operations:** We can create, read, update and delete infrastructure resources through Terraform commands instead of manually performing operations through the cloud console.

3\. **Consistency:** We can reuse the same Terraform code across DEV, SIT, UAT and PROD environments with different configurations, reducing environment inconsistencies.

4\. **Inventory Management:** We can understand which infrastructure resources are managed through Terraform by examining configuration files and Terraform state.

5\. **Cost Optimization:** We can automate infrastructure start and stop schedules for non-production environments using AWS EventBridge Scheduler and Lambda.

6\. **Dependency Management:** Terraform automatically identifies dependencies between resources and creates them in the correct order. We can also define explicit dependencies using `depends_on`.

7\. **Modules:** Terraform modules help us create reusable infrastructure code, reduce duplication and maintain standard infrastructure across multiple environments.

Overall, IaC makes infrastructure provisioning automated, consistent, repeatable and easier to manage."

## **5. Important interview questions to prepare**

These are the follow-up questions I recommend learning after this topic.

**Interview revision**

0/7 answers revealed

1What is the difference between Terraform and Ansible?

2What is Terraform state file?

3What happens if someone manually changes an EC2 instance in AWS Console?

4What is the difference between Terraform plan and apply?

5What is the difference between Terraform resource and module?

6Can Terraform manage infrastructure created manually?

7What is infrastructure drift?

Reveal all answers

**My recommendation:** For interviews, don't just memorize the seven advantages. Be prepared to explain each one with a real AWS example and Terraform command. That demonstrates practical knowledge rather than just theoretical understanding.
