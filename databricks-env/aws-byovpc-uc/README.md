# Databricks AWS Environment Setup — BYOVPC + Unity Catalog

This Terraform configuration deploys a complete Databricks environment on AWS:

- **VPC** with private/public/intra subnets, NAT Gateway, and VPC endpoints (S3, STS, Kinesis)
- **Security group** with Databricks-required egress rules
- **Cross-account IAM role** for workspace provisioning
- **Root S3 bucket** for workspace storage (DBFS)
- **Databricks E2 workspace**
- **Unity Catalog metastore** (new or existing) assigned to the workspace
- **Unity Catalog catalog** with its own IAM role, S3 bucket, storage credential, and external location (optional, `new_catalog = true`)
- **UC-compatible single-node cluster** for deployment validation (optional, `new_cluster = true`)

## Prerequisites

- Terraform ~> 1.3
- AWS CLI configured
- Databricks account (E2) with account admin access
- AWS permissions: VPC, IAM, S3, Security Group
- Databricks service principal with `DATABRICKS_CLIENT_ID` / `DATABRICKS_CLIENT_SECRET`

## Quick Start

```bash
cd tf/

# 1. Copy and fill in your variables
cp terraform.tfvars.example terraform.tfvars
# Edit terraform.tfvars with your account ID, AWS account ID, region, CIDRs, etc.

# 2. Set credentials
export AWS_ACCESS_KEY_ID="..."
export AWS_SECRET_ACCESS_KEY="..."
export DATABRICKS_CLIENT_ID="..."
export DATABRICKS_CLIENT_SECRET="..."

# 3. Deploy
terraform init
terraform plan
terraform apply

# 4. Get workspace URL
terraform output workspace_url
```

Deployment typically takes 10–15 minutes.

## File Structure

| File | Purpose |
|------|---------|
| `versions.tf` | Provider version constraints |
| `providers.tf` | AWS + Databricks (account + workspace) providers |
| `variables.tf` | All input variables |
| `outputs.tf` | All outputs (workspace URL/ID, VPC, metastore, catalog, etc.) |
| `terraform.tfvars.example` | Example variable values |
| `network.tf` | VPC, subnets, NAT, VPC endpoints |
| `security_group.tf` | Databricks security group rules |
| `credential.tf` | Cross-account IAM role |
| `root_s3_bucket.tf` | Workspace root S3 bucket |
| `workspace.tf` | E2 workspace + MWS network/credentials/storage |
| `metastore.tf` | Unity Catalog metastore + workspace assignment |
| `unity_catalog.tf` | Storage credential, external location, catalog |
| `cluster.tf` | Optional UC single-node validation cluster |

## Clean Up

```bash
terraform destroy
```
