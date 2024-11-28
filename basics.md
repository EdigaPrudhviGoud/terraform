Variables:
# Defining an input variable for AWS region
variable "aws_region" {
  description = "The AWS region to create resources in"
  type        = string
  default     = "us-west-2"  # You can also provide a default value
}

# Defining an input variable for EC2 instance type
variable "instance_type" {
  description = "The type of EC2 instance to create"
  type        = string
  default     = "t2.micro"
}

provider "aws" {
  region = var.aws_region  # Reference the input variable
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"  # Use a valid AMI ID
  instance_type = var.instance_type  # Reference the input variable

  tags = {
    Name = "MyEC2Instance"
  }
}

2. Assigning Values to Input Variables
i)Command-line arguments when running terraform apply using the -var flag.
        terraform apply -var="aws_region=us-east-1" -var="instance_type=t2.large"
ii)terraform.tfvars
aws_region    = "us-east-1"
instance_type = "t2.large"
iii)Using env var.
export TF_VAR_aws_region="us-east-1"
export TF_VAR_instance_type="t2.large"
_________________________________________________________________________________________________________________________________________________
Output Variables in Terraform:output.tf
output "instance_id" {
  description = "The ID of the EC2 instance"
  value       = aws_instance.example.id  # Referencing a resource's attribute
}

output "instance_public_ip" {
  description = "The public IP address of the EC2 instance"
  value       = aws_instance.example.public_ip  # Referencing a resource's attribute
}

terraform output # show the output values
------------------------------------------------------------------------------------------------------------------------------
We can give different variables to different environments by creating different tfvars files
terraform.tfvars
prod.tfvars
dev.tfvars
terraform apply --varfile prod.tfvars --auto-approve
