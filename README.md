# AWS Terraform module which runs app on AWS Fargate

[app](https://www.runapp.io/) is tool which provides unified workflow for collaborating on Terraform through GitHub, GitLab and Bitbucket Cloud.

This repository contains Terraform infrastructure code which creates AWS resources required to run [app](https://www.runapp.io/) on AWS, including:

- Application Load Balancer (ALB)
- Domain name using AWS Route53 which points to ALB
- [AWS Elastic Cloud Service (ECS)](https://aws.amazon.com/ecs/) and [AWS Fargate](https://aws.amazon.com/fargate/) running app Docker image
- AWS Parameter Store to keep secrets and access them in ECS task natively

[AWS Fargate](https://aws.amazon.com/fargate/)

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 0.14.0 |
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | >= 3.36.0, < 4.0 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | >= 3.36.0, < 4.0 |

## Modules

| Name | Source | Version |
|------|--------|---------|
| <a name="module_alb"></a> [alb](#module\_alb) | terraform-aws-modules/alb/aws | v5.13.0 |
| <a name="module_alb_http_sg"></a> [alb\_http\_sg](#module\_alb\_http\_sg) | terraform-aws-modules/security-group/aws | v3.18.0 |
| <a name="module_alb_https_sg"></a> [alb\_https\_sg](#module\_alb\_https\_sg) | terraform-aws-modules/security-group/aws | v3.18.0 |
| <a name="module_app_sg"></a> [app\_sg](#module\_app\_sg) | terraform-aws-modules/security-group/aws | v3.18.0 |
| <a name="module_container_definition"></a> [container\_definition](#module\_container\_definition) | cloudposse/ecs-container-definition/aws | v0.58.1 |

## Resources

| Name | Type |
|------|------|
| [aws_cloudwatch_log_group.app](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/cloudwatch_log_group) | resource |
| [aws_ecs_service.app](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecs_service) | resource |
| [aws_ecs_task_definition.app](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecs_task_definition) | resource |
| [aws_iam_role.ecs_task_execution](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_iam_role_policy.ecs_task_access_backend](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy) | resource |
| [aws_iam_role_policy.ecs_task_access_secrets](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy) | resource |
| [aws_iam_role_policy_attachment.ecs_task_execution](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy_attachment) | resource |
| [aws_route53_record.app](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route53_record) | resource |
| [aws_caller_identity.current](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/caller_identity) | data source |
| [aws_ecs_task_definition.app](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/ecs_task_definition) | data source |
| [aws_iam_policy_document.ecs_task_access_backend](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.ecs_task_access_secrets](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.ecs_tasks](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_region.current](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/region) | data source |
| [aws_route53_zone.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/route53_zone) | data source |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_alb_ingress_cidr_blocks"></a> [alb\_ingress\_cidr\_blocks](#input\_alb\_ingress\_cidr\_blocks) | Comma separated string of IPv4 CIDR ranges to use on all ingress rules of the ALB. | `string` | `"0.0.0.0/0"` | no |
| <a name="input_alb_log_bucket_name"></a> [alb\_log\_bucket\_name](#input\_alb\_log\_bucket\_name) | S3 bucket (externally created) for storing load balancer access logs. Required if alb\_logging\_enabled is true. | `string` | `""` | no |
| <a name="input_alb_log_location_prefix"></a> [alb\_log\_location\_prefix](#input\_alb\_log\_location\_prefix) | S3 prefix within the log\_bucket\_name under which logs are stored. | `string` | `""` | no |
| <a name="input_alb_logging_enabled"></a> [alb\_logging\_enabled](#input\_alb\_logging\_enabled) | Controls if the ALB will log requests to S3. | `bool` | `false` | no |
| <a name="input_app_fqdn"></a> [app\_fqdn](#input\_app\_fqdn) | FQDN of app to use. Set this only to override Route53 and ALB's DNS name. | `string` | `null` | no |
| <a name="input_app_image"></a> [app\_image](#input\_app\_image) | Docker image to run app with. If not specified, official app image will be used | `string` | `""` | no |
| <a name="input_app_port"></a> [app\_port](#input\_app\_port) | Local port app should be running on. Default value is most likely fine. | `number` | `4141` | no |
| <a name="input_certificate_arn"></a> [certificate\_arn](#input\_certificate\_arn) | ARN of certificate issued by AWS ACM. If empty, a new ACM certificate will be created and validated using Route53 DNS | `string` | n/a | yes |
| <a name="input_cloudwatch_log_retention_in_days"></a> [cloudwatch\_log\_retention\_in\_days](#input\_cloudwatch\_log\_retention\_in\_days) | Retention period of app CloudWatch logs | `number` | `7` | no |
| <a name="input_container_memory_reservation"></a> [container\_memory\_reservation](#input\_container\_memory\_reservation) | The amount of memory (in MiB) to reserve for the container | `number` | `128` | no |
| <a name="input_create_route53_record"></a> [create\_route53\_record](#input\_create\_route53\_record) | Whether to create Route53 record for app | `bool` | `true` | no |
| <a name="input_custom_container_definitions"></a> [custom\_container\_definitions](#input\_custom\_container\_definitions) | A list of valid container definitions provided as a single valid JSON document. By default, the standard container definition is used. | `string` | `""` | no |
| <a name="input_custom_environment_secrets"></a> [custom\_environment\_secrets](#input\_custom\_environment\_secrets) | List of additional secrets the container will use (list should contain maps with `name` and `valueFrom`) | <pre>list(object(<br>    {<br>      name      = string<br>      valueFrom = string<br>    }<br>  ))</pre> | `[]` | no |
| <a name="input_custom_environment_variables"></a> [custom\_environment\_variables](#input\_custom\_environment\_variables) | List of additional environment variables the container will use (list should contain maps with `name` and `value`) | <pre>list(object(<br>    {<br>      name  = string<br>      value = string<br>    }<br>  ))</pre> | `[]` | no |
| <a name="input_ecs_cluster_id"></a> [ecs\_cluster\_id](#input\_ecs\_cluster\_id) | The ECS cluster ID | `any` | n/a | yes |
| <a name="input_ecs_service_assign_public_ip"></a> [ecs\_service\_assign\_public\_ip](#input\_ecs\_service\_assign\_public\_ip) | Should be true, if ECS service is using public subnets (more info: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_cannot_pull_image.html) | `bool` | `false` | no |
| <a name="input_ecs_service_deployment_maximum_percent"></a> [ecs\_service\_deployment\_maximum\_percent](#input\_ecs\_service\_deployment\_maximum\_percent) | The upper limit (as a percentage of the service's desiredCount) of the number of running tasks that can be running in a service during a deployment | `number` | `200` | no |
| <a name="input_ecs_service_deployment_minimum_healthy_percent"></a> [ecs\_service\_deployment\_minimum\_healthy\_percent](#input\_ecs\_service\_deployment\_minimum\_healthy\_percent) | The lower limit (as a percentage of the service's desiredCount) of the number of running tasks that must remain running and healthy in a service during a deployment | `number` | `50` | no |
| <a name="input_ecs_service_desired_count"></a> [ecs\_service\_desired\_count](#input\_ecs\_service\_desired\_count) | The number of instances of the task definition to place and keep running | `number` | `1` | no |
| <a name="input_ecs_task_cpu"></a> [ecs\_task\_cpu](#input\_ecs\_task\_cpu) | The number of cpu units used by the task | `number` | `256` | no |
| <a name="input_ecs_task_memory"></a> [ecs\_task\_memory](#input\_ecs\_task\_memory) | The amount (in MiB) of memory used by the task | `number` | `512` | no |
| <a name="input_environment"></a> [environment](#input\_environment) | The name of the environment | `string` | n/a | yes |
| <a name="input_health_check_healthy_threshold"></a> [health\_check\_healthy\_threshold](#input\_health\_check\_healthy\_threshold) | Healthcheck interval | `number` | `3` | no |
| <a name="input_health_check_http_code_matcher"></a> [health\_check\_http\_code\_matcher](#input\_health\_check\_http\_code\_matcher) | Healthcheck interval | `string` | `"200-399"` | no |
| <a name="input_health_check_interval"></a> [health\_check\_interval](#input\_health\_check\_interval) | Healthcheck interval | `number` | `15` | no |
| <a name="input_health_check_path"></a> [health\_check\_path](#input\_health\_check\_path) | Healthcheck interval | `string` | `"/"` | no |
| <a name="input_health_check_timeout"></a> [health\_check\_timeout](#input\_health\_check\_timeout) | Healthcheck interval | `number` | `5` | no |
| <a name="input_health_check_unhealthy_threshold"></a> [health\_check\_unhealthy\_threshold](#input\_health\_check\_unhealthy\_threshold) | Healthcheck interval | `number` | `4` | no |
| <a name="input_internal"></a> [internal](#input\_internal) | Whether the load balancer is internal or external | `bool` | `false` | no |
| <a name="input_lb_extra_security_group_ids"></a> [lb\_extra\_security\_group\_ids](#input\_lb\_extra\_security\_group\_ids) | List of one or more security groups to be added to the load balancer | `list(string)` | `[]` | no |
| <a name="input_name"></a> [name](#input\_name) | Name to use on all resources created (VPC, ALB, etc) | `string` | `"app"` | no |
| <a name="input_policies_arn"></a> [policies\_arn](#input\_policies\_arn) | A list of the ARN of the policies you want to apply | `list(string)` | <pre>[<br>  "arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy"<br>]</pre> | no |
| <a name="input_private_subnet_ids"></a> [private\_subnet\_ids](#input\_private\_subnet\_ids) | A list of IDs of existing private subnets inside the VPC | `list(string)` | `[]` | no |
| <a name="input_public_subnet_ids"></a> [public\_subnet\_ids](#input\_public\_subnet\_ids) | A list of IDs of existing public subnets inside the VPC | `list(string)` | `[]` | no |
| <a name="input_route53_record_name"></a> [route53\_record\_name](#input\_route53\_record\_name) | Name of Route53 record to create ACM certificate in and main A-record. If null is specified, var.name is used instead. Provide empty string to point root domain name to ALB. | `string` | `null` | no |
| <a name="input_route53_zone_name"></a> [route53\_zone\_name](#input\_route53\_zone\_name) | Route53 zone name to create ACM certificate in and main A-record, without trailing dot | `string` | n/a | yes |
| <a name="input_tags"></a> [tags](#input\_tags) | A map of tags to use on all resources | `map(string)` | n/a | yes |
| <a name="input_vpc_id"></a> [vpc\_id](#input\_vpc\_id) | ID of an existing VPC where resources will be created | `string` | `""` | no |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_alb_dns_name"></a> [alb\_dns\_name](#output\_alb\_dns\_name) | Dns name of alb |
| <a name="output_alb_zone_id"></a> [alb\_zone\_id](#output\_alb\_zone\_id) | Zone ID of alb |
| <a name="output_app_url"></a> [app\_url](#output\_app\_url) | URL of app |
| <a name="output_ecs_security_group"></a> [ecs\_security\_group](#output\_ecs\_security\_group) | Security group assigned to ECS Service in network configuration |
| <a name="output_ecs_task_definition"></a> [ecs\_task\_definition](#output\_ecs\_task\_definition) | Task definition for ECS service (used for external triggers) |
| <a name="output_task_role_arn"></a> [task\_role\_arn](#output\_task\_role\_arn) | The app ECS task role arn |
| <a name="output_vpc_id"></a> [vpc\_id](#output\_vpc\_id) | ID of the VPC that was created or passed in |
<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->