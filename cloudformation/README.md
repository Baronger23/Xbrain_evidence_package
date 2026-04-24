# SportFields W3 CloudFormation Bonus

This directory contains a CloudFormation version of the SportFields W3 stack for the bonus item:

`Full CloudFormation coverage — all W3 infrastructure in one working CFN stack.`

The template is intentionally separate from the live AWS CLI stack. Use a different `ResourceSuffix`, such as `truc-cfn`, if you deploy it, so it does not overwrite the running `sportfields-dev-truc` resources.

## Coverage

`sportfields-w3-full.yaml` covers the W3 resource set:

- VPC with public, private application, and private database tiers
- Internet Gateway, one NAT Gateway, private route tables, and S3 Gateway Endpoint
- Security groups for ALB, EC2, RDS, and Redis
- Private S3 buckets with Block Public Access, versioning, and encryption
- RDS PostgreSQL Multi-AZ with 7-day backup retention
- ElastiCache Redis replication group with Multi-AZ failover
- ALB, target group, HTTPS listener, EC2 launch template, and ASG
- Lambda `kb-sync` with an S3 event ingestion handler
- Bedrock Knowledge Base with S3 data source and S3 Vectors storage

## Validate

```bash
aws cloudformation validate-template \
  --template-body file://infra/cloudformation/sportfields-w3-full.yaml \
  --region us-west-2
```

Use the validation output as Evidence Pack section 8 if you are only claiming the `Partial CloudFormation template` bonus.

## Deploy For Full Bonus

Use a suffix that does not collide with the live stack:

```bash
aws cloudformation deploy \
  --stack-name sportfields-dev-w3-full-truc-cfn \
  --template-file infra/cloudformation/sportfields-w3-full.yaml \
  --parameter-overrides \
    ProjectName=sportfields \
    EnvironmentName=dev \
    ResourceSuffix=truc-cfn \
    BackendArtifactBucket=sportfields-deployments-dev-truc \
    BackendArtifactKey=backend/releases/latest.tar.gz \
    BackendEnvSsmParameter=/sportfields/dev/truc/backend/env \
    BackendAcmCertificateArn=arn:aws:acm:us-west-2:645314756823:certificate/REPLACE_ME \
    DbPassword=REPLACE_WITH_TEMP_PASSWORD \
  --capabilities CAPABILITY_NAMED_IAM \
  --region us-west-2
```

This creates billable resources. Delete the bonus stack after screenshots if you do not need it running:

```bash
aws cloudformation delete-stack \
  --stack-name sportfields-dev-w3-full-truc-cfn \
  --region us-west-2
```

## Evidence Pack Section 8

Capture:

- Pre-state: no `sportfields-dev-w3-full-truc-cfn` stack exists.
- Action: `validate-template` output and, for full bonus, `cloudformation deploy` command.
- Post-state: stack `CREATE_COMPLETE`, outputs for VPC, DB endpoint, Redis endpoint, KB ID, and data source ID.
- Measurement: stack creation duration.
- Reflection: one short note on what CloudFormation made repeatable compared with ad-hoc CLI.
