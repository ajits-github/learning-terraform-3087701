Explanation of main.tf file:

1. **Data Sources: AWS AMI and VPC**
   
   ```hcl
   data "aws_ami" "app_ami" {
     ...
   }
   ```

   This block fetches the ID of a specific Amazon Machine Image (AMI), which is a pre-configured virtual machine image. The filters ensure that we get the most recent image with a specific naming pattern and that it's an HVM virtualization type. The "data" keyword denotes this is a data source, not a direct resource.

   ```hcl
   data "aws_vpc" "default" {
     default = true
   }
   ```

   This block fetches the ID of the default Virtual Private Cloud (VPC) in your AWS environment. VPCs allow you to provision a logically isolated section of the AWS Cloud.

2. **AWS Instance Resource**

   ```hcl
   resource "aws_instance" "blog" {
     ...
   }
   ```

   This block provisions an EC2 instance. The keyword "resource" indicates we're creating or managing an infrastructure resource. 
   
   - `ami`: Specifies which AMI to use for the instance. We refer to the data source for the AMI we fetched earlier.
   - `instance_type`: Refers to the type or size of the instance, like `t2.micro`, which is passed as a variable.
   - `vpc_security_group_ids`: List of security groups to be associated with the instance. Here, we reference the security group defined later in the file.
   - `tags`: Assigns a name tag to the instance.

3. **AWS Security Group Resource**

   ```hcl
   resource "aws_security_group" "blog" {
     ...
   }
   ```

   A security group acts as a virtual firewall to control inbound and outbound traffic. 

   - `name`: Specifies the name of the security group.
   - `description`: A brief description of the security group.
   - `vpc_id`: Associates the security group with a specific VPC. Here, we reference the default VPC we fetched earlier.

4. **AWS Security Group Rules**

   ```hcl
   resource "aws_security_group_rule" "blog_http_in" {
     ...
   }
   ```

   Security group rules define the allowed or denied traffic. The above rule allows inbound HTTP traffic on port 80.

   ```hcl
   resource "aws_security_group_rule" "blog_https_in" {
     ...
   }
   ```

   Similarly, this rule allows inbound HTTPS traffic on port 443.

   ```hcl
   resource "aws_security_group_rule" "blog_everything_out" {
     ...
   }
   ```

   This rule permits all outbound traffic from the instance.

For each of these blocks:

- The first quoted string following the block keyword (`data` or `resource`) is the **type** of resource we're working with. This is intrinsic to the kind of cloud resource you want and cannot be changed.
  
- The second quoted string is the **local name** for this resource. It's how you will refer to this particular resource block elsewhere in your Terraform code.

- Inside the block `{...}` are the **configuration arguments** for the resource. The specific arguments and their meanings differ between types of resources. 

In summary, this Terraform script will:
1. Fetch information about a specific AMI and default VPC.
2. Create an EC2 instance using the fetched AMI.
3. Create a security group and attach it to the instance.
4. Define rules in the security group to allow inbound HTTP/HTTPS traffic and allow all outbound traffic.
   
-----------------------------------------------

In Terraform, there's a distinction between **resources** and **data sources**:

1. **Resource**:
   - Represents an infrastructure component (like a virtual machine, security group, or database) that you want to create, manage, or destroy with Terraform.
   - Terraform will track the state of this resource across runs, ensuring it matches what you've defined in your code. If the actual state in the infrastructure diverges from what's in the code, Terraform will make necessary changes to reconcile them.
   - In the Terraform code, you use the `resource` block to define a resource.

2. **Data Source**:
   - Represents a way to fetch data or information from your cloud provider or other services which can then be used in your Terraform configurations.
   - This is especially useful when you want to use existing infrastructure components or when you need data that isn't known ahead of time. 
   - Terraform doesn't manage the lifecycle of data sources. Instead, it queries for the information as needed.
   - In the Terraform code, you use the `data` block to define a data source.

To provide a simple analogy:

- Think of a **resource** as a plant you're growing in a garden. You plant it, care for it, and decide when to remove it.
  
- A **data source**, on the other hand, is like checking the weather. You're just getting information about the current state of things, but you aren't directly affecting or controlling the weather.

In the Terraform file- main.tf:

- `data "aws_ami" "app_ami"` fetches information about a specific Amazon Machine Image (AMI). It doesn't create or manage an AMI; it just provides details about one that already exists.

- `resource "aws_instance" "blog"` defines an AWS EC2 instance that Terraform will create, update, or destroy based on the configuration.
