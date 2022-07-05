# aws-the-hard-way

aws-the-hard-way is modeled after https://github.com/kelseyhightower/kubernetes-the-hard-way. It's a recognition that engineers learn best when they explore tech hands-on-keyboard.

## Topics:

- [AWS Organizations and Accounts](modules/organizations-and-accounts/overview.md)
- AWS IAM
- AWS STS
- VPC - Standard
- VPC - PrivateLink
- LoadBalancers
- Route53 Hosted Zones

## Target Audience:

Site Reliability Engineers supporting Red Hat OpenShift on AWS (ROSA) and OpenShift Dedicated (OSD)

## Contributing

Each module should include:

- An overview of a specific AWS service as is used by SRE in OSD/ROSA. This will reference existing AWS documentation where possible.
- A guided tutorial to interact with the service in “everyday” scenarios using the AWS CLI. Not all details will be spelled out to challenge the user, e.g. passing in a profile is assumed.
- A lab with an initial script that will set up something “broken” or “misconfigured” related to the AWS service in the module that the user must fix.
