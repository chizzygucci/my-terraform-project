## Troubleshooting Terraform EKS Setup

During my journey setting up Amazon EKS using Terraform, I encountered several real-world infrastructure issues and resolved them systematically.
Below are some of the key challenges and how I resolved them:

---

###  1. Plugin Did Not Respond / TLS Provider Error

**Problem:**


Plugin did not respond: failed to respond to the plugin.(*GRPCProvider).GetProviderSchema call.


**Cause**:
This occurred due to a mismatch between the Terraform core version and some provider plugins (tls, time, etc.), especially when using Terraform v1.8.5.

**Solution**:

Reinstalled Terraform using a stable version (v1.6.6) compatible with all required providers.

Cleaned up the environment using:rm -rf .terraform .terraform.lock.hcl
terraform init
Re-installed providers and validated configuration.




 ##  2. KMS Alias Already Exists

**Problem:**


**Error**: creating KMS Alias (alias/eks/flask-eks-cluster): AlreadyExistsException


**Cause**:
The KMS alias was already created — either manually or during a failed terraform apply. Terraform could not proceed because the local state file did not reflect this existing resource on AWS.

**Solution**:

To resolve this, the alias was imported into the Terraform state using the following command:
 terraform import module.eks.module.kms.aws_kms_alias.this["cluster"] alias/eks/flask-eks-cluster



##  CloudWatch Log Group Already Exists

**Problem:**

ResourceAlreadyExistsException: The specified log group already exists

**Cause**:
Terraform attempted to create a CloudWatch Log Group that was already provisioned from a previous run or manually via the AWS Console. Because Terraform did not manage it in the state file, it treated the existing log group as a conflict.

**Solution**:
The existing CloudWatch Log Group was imported into the Terraform state using:
terraform import module.eks.aws_cloudwatch_log_group.this[0] /aws/eks/flask-eks-cluster/cluster
his brought the log group under Terraform’s control, ensuring future runs wouldn’t attempt to recreate it and cause further errors.
