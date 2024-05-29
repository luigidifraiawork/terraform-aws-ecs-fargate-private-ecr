# AWS ECS Fargate access to private ECR

The material provided in this repository creates a VPC with multiple Endpoints, showing that a standalone ECS Task can list the contents of an S3 bucket without Internet access and with a tight policy for the S3 Gateway Endpoint, in line with the principle of least privilege. In particular, it exposes the existence and use of the following resource:

```text
"arn:aws:s3:::prod-${local.region}-starport-layer-bucket/*"
```

For further information, consult the [ECR documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/vpc-endpoints.html#ecr-minimum-s3-perms).

The material *explicitly* avoids using atomic resources defined in the AWS provider, with the exception of data sources. Instead, it uses *exclusively* AWS modules and submodules managed by [Anton Babenko](https://registry.terraform.io/namespaces/antonbabenko) in the Hashicorp Terraform Registry.

This work was not accepted as an [example for the AWS ECS Terraform module](https://github.com/terraform-aws-modules/terraform-aws-ecs/pull/191) so I am sharing it here.

## Deployment

Set up authentication details to your Sandbox (e.g. *A Cloud Guru*) with:
```commandline
export AWS_ACCESS_KEY_ID=AKIAZR3FF5EXAMPLEID
export AWS_SECRET_ACCESS_KEY=k9eabHZx0Kq0utFZ2u20Ymo1I7zaWEXAMPLEKEY
```

Set the default region with:
```commandline
export AWS_DEFAULT_REGION=us-east-1
```

Ensure the above are correct by running:
```commandline
aws sts get-caller-identity
```

The output should look as per below:
```commandline
{
    "UserId": "AIDAZR3FF5MEXAMPLEID",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/cloud_user"
}
```

Deploy with:
```commandline
terraform init
terraform plan
terraform apply -auto-approve
```

Destroy with:
```commandline
terraform destroy -auto-approve
```

<!-- BEGIN_TF_DOCS -->
## Requirements

| Name | Version |
|------|---------|
| terraform | >= 1.0 |
| aws | >= 4.66 |
| random | >= 3.4 |

## Providers

| Name | Version |
|------|---------|
| aws | >= 4.66 |
| random | >= 3.4 |

## Modules

| Name | Source | Version |
|------|--------|---------|
| ecr | terraform-aws-modules/ecr/aws | ~> 2.2 |
| ecs_cluster | terraform-aws-modules/ecs/aws//modules/cluster | ~> 5.11 |
| ecs_task_definition | terraform-aws-modules/ecs/aws//modules/service | ~> 5.11 |
| kms_bucket | terraform-aws-modules/kms/aws | ~> 2.2 |
| kms_cloudwatch | terraform-aws-modules/kms/aws | ~> 2.2 |
| s3_bucket | terraform-aws-modules/s3-bucket/aws | ~> 4.1 |
| security_group_vpc_endpoint_interface | terraform-aws-modules/security-group/aws | ~> 5.1 |
| vpc | terraform-aws-modules/vpc/aws | ~> 5.0 |
| vpc_endpoints | terraform-aws-modules/vpc/aws//modules/vpc-endpoints | ~> 5.7 |

## Resources

| Name | Type |
|------|------|
| [random_pet.this](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/pet) | resource |
| [aws_availability_zones.available](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/availability_zones) | data source |
| [aws_caller_identity.current](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/caller_identity) | data source |
| [aws_iam_policy_document.bucket](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.s3_endpoint](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |

## Inputs

No inputs.

## Outputs

| Name | Description |
|------|-------------|
| cluster_arn | ARN that identifies the cluster |
| cluster_autoscaling_capacity_providers | Map of capacity providers created and their attributes |
| cluster_capacity_providers | Map of cluster capacity providers attributes |
| cluster_id | ID that identifies the cluster |
| cluster_name | Name that identifies the cluster |
| private_ecr_repository_push_commands | Commands to push the awscli container image to the private ECR repository |
| s3_bucket_upload_command | Command to upload files to the example S3 bucket |
| task_definition_run_task_command | awscli command to run the standalone task |
<!-- END_TF_DOCS -->
