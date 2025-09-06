# SurlesMobile Infrastructure

AWS CloudFormation infrastructure for the SurlesMobile portfolio application.

## Architecture Overview

This infrastructure stack deploys:
- **Cognito User Pool** with admin/editor/viewer groups
- **CloudFront + S3** for Angular application hosting
- **RDS PostgreSQL** database with backup and monitoring
- **ECS Fargate** cluster for .NET API with Application Load Balancer
- **VPC** with public/private subnets across 2 AZs
- **Security Groups** and IAM roles following least privilege principle

## Prerequisites

- AWS CLI configured with appropriate permissions
- AWS account with necessary service limits
- Domain name (optional, for custom SSL certificate)

## Deployment

### Using GitHub Actions (Recommended)

1. Configure GitHub repository secrets:
   ```
   AWS_ACCESS_KEY_ID
   AWS_SECRET_ACCESS_KEY
   AWS_REGION
   ```

2. Push to main branch to trigger deployment
3. Monitor deployment in GitHub Actions tab

### Manual Deployment

```bash
# Deploy the stack
aws cloudformation create-stack \
  --stack-name surlesmobile-infra \
  --template-body file://cloudformation/main.yaml \
  --parameters file://cloudformation/parameters.json \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM

# Update existing stack
aws cloudformation update-stack \
  --stack-name surlesmobile-infra \
  --template-body file://cloudformation/main.yaml \
  --parameters file://cloudformation/parameters.json \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM
```

## Parameters

Key parameters to configure in `cloudformation/parameters.json`:

- **Environment**: dev, staging, prod
- **DBPassword**: Secure password for RDS instance
- **DomainName**: Your domain name (optional)
- **CertificateArn**: SSL certificate ARN (if using custom domain)

## Outputs

The stack provides these outputs for other services:
- **CognitoUserPoolId**: For API authentication
- **CognitoUserPoolClientId**: For frontend authentication
- **ALBDNSName**: API endpoint
- **CloudFrontDomainName**: Frontend URL
- **RDSEndpoint**: Database connection string

## Security Considerations

- RDS instance in private subnets only
- All traffic encrypted in transit and at rest
- Cognito with secure password policy
- Security groups with minimal required access
- CloudTrail logging enabled
- VPC Flow Logs enabled

## Monitoring

- CloudWatch alarms for RDS, ECS, and ALB
- Application Performance Monitoring via CloudWatch Insights
- Cost monitoring with billing alarms

## Cleanup

```bash
aws cloudformation delete-stack --stack-name surlesmobile-infra
```

**Note**: This will delete all resources. Ensure you have backups if needed.