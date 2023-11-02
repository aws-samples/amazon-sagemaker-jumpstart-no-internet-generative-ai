# amazon-sagemaker-jumpstart-no-internet-access-blog


## Getting started
This code sample accompanies the blog post [Using Generative AI foundation models in VPC mode with no internet connectivity using Amazon SageMaker JumpStart](https://aws.amazon.com/blogs/machine-learning/). Please follow the steps in the blog post to create the AWS CloudFormation stacks using the templates provided.

## Clean up
You will be creating several billable resources in your AWS account. After experimentation, make sure you follow the steps in [CLEANUP.md](CLEANUP.md) to remove all resources created. If you encounter any issues when deleting the resources, troubleshoot the issues by following the [AWS CloudFormation troubleshooting guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html).

## If you want to add further restrictions to network ACLs and security groups

Even though this sample configures SageMaker Studio in a VPC with no internet access, you may want to add further restrictions to network traffic using network ACLs and security groups. To learn more about network ACLs and security groups, read [Compare security groups and network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/infrastructure-security.html#VPC_Security_Comparison).

This sample does not modify the default network ACLs associated with the subnets. The [default network ACL](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#default-network-acl) is configured to allow all traffic to flow in and out of the subnets with which it is associated. If you want to restrict the subnets to accept only the traffic originated from the VPC SageMaker Studio is configured to use, please note the following:

If you change the network ACLs to allow traffic to/from the VPC CIDR only, the connectivity to services via interface VPC endpoints will not be affected, as [interface VPC endpoints create an elastic network interface inside the VPC subnets](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html). However, the gateway VPC endpoint for S3 does not create elastic network interfaces. Instead, [the gateway VPC endpoints add prefix lists to the route tables](https://docs.aws.amazon.com/vpc/latest/privatelink/gateway-endpoints.html) and the traffic from SageMaker Studio to Amazon S3, which is going to the public address range for Amazon S3 in the current region, will not match the network ACL rules that allows traffic to/from the VPC CIDR only. As a result, you will not be able to see the list of SageMaker JumpStart models when you launch SageMaker Studio.

To address the issue, you have two options:

1. Add outbound/inbound rules to allow traffic to/from all IP address ranges for Amazon S3 in the region as documented [here](https://docs.aws.amazon.com/vpc/latest/userguide/aws-ip-ranges.html).
2. For Amazon S3, use interface VPC endpoints instead of gateway VPC endpoints. By using interface VPC endpoints, you can restrict the network ACLs without adding IP address ranges. However, note that interface VPC endpoints are chargeable. For more information, read the blog post [Choosing Your VPC Endpoint Strategy for Amazon S3](https://aws.amazon.com/blogs/architecture/choosing-your-vpc-endpoint-strategy-for-amazon-s3/).

In addition to restricting network ACLs, if you want to restrict outbound traffic from SageMaker Studio using  security groups, you can add an outbound rule to the security group to allow traffic to the VPC CIDR only. If you are using gateway S3 endpoints, you should configure the security group to allow outbound traffic to all IP ranges for Amazon S3 in the current region.
