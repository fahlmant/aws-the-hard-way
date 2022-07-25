# AWS Identity and Access Management (IAM)

## Intro

AWS IAM is the service for determining who can do what in an AWS account. Its the basis of everything done in AWS for SREP,
including AWS Account creation and setup (via aws-account-operator), SRE access to AWS accounts in CCS and non-CCS (via osdctl 
and ocm backplane cloud), and operating an STS cluster (via cluster operator roles). IAM is composed of the who, using users and  assumed roles, and what they can do, using policies. 

Please read the following docs to get started:

[IAM Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html) 

Key points to understand:
- User ARNs
- Access Keys
- Console password access


[IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)

Key points to understand:
- Difference between a role and a user
- How to assume a role
- How role ARNs differ from user ARNs

[IAM Policies and Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)

Key points to understand:
- AWS Managed Policies vs Customer Managed Policies
- Inline Policies
- How SCPs affect permissions
- JSON Policy doc structure
- How to control access to resources using tags


## SREP Usage

The most common places we use IAM permissions is
1. In each organization root account (osd-staging-1, osd-staging-2 and rhcontrol), each SREP member gets a personal IAM User using their kerberos name. 
2. To gain access to the AWS account of a cluster or of an assigned dev account, a role is assumed in the tareget account. 

The roles used for AWS accounts that correspond to a dev account use a special role that AWS provides in each account when created as part of an organization. This role is named `OrganizationAccountAccessRole`. Read [this doc](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_access.html#orgs_manage_accounts_create-cross-account-role) for more details. 

To access AWS accounts for a CCS cluster, a series of roles are assumed in a chain, and  requires the individual IAM user in the root account to start the process. This exact chain will be covered in the STS module. 

The installation process, as well as cluster operations that require AWS API calls all use either an IAM user or Role to perform those actions. 

AWS IAM is used everywhere in OSD/ROSA, and its a fundamental service to understand. Let's learn a little more about the underlying API calls in the [next section](cli-practice.md)