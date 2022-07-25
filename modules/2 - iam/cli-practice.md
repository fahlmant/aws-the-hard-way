# Common CLI actions for AWS IAM

All IAM API calls can be seen [on the cli reference page](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/index.html).

1. Pre-requisite: Gain access to your dev account by running `osdctl account cli -i $accountID -p osd-staging-2`. If you don't know your account ID, you can get it by running `osdctl account mgmt list -u $kerberosID -p osd-staging-2`. Using the access key, secret key and session token from the output, run the following command:
```
$ AWS_ACCESS_KEY_ID=$accessKey AWS_SECRET_ACCESS_KEY=$secretKey $AWS_SESSION_TOKEN=$token aws sts get-caller-identity 
```

Your output should look something like this:
```
{
    "UserId": "AROAU4GZCQTXJOWX2WGWB:AIDAIFSL3RQLR6XXDVO42",
    "Account": "335462696174",
    "Arn": "arn:aws:sts::335462696174:assumed-role/OrganizationAccountAccessRole/AIDAIFSL3RQLR6XXDVO42"
}
```

We'll cover the details of this in the next module - STS. For now, just ensure that your output matches the above. but with your dev account ID.

2. Let's list the roles that exist in this account. Remember to include the Access Key, Secret Key and Session Token!
```
$ aws iam list-roles
```

There's a lot of JSON output here. To make it easier let's just get the names of the roles

```
$ aws iam list-roles | jq -r '.Roles[].RoleName'
```
This selects the RoleName field out of each Role object in the Role array. Let's pick a name out and get a bit more information about that role.

3. AWS creates a number of roles in an AWS account when it is first created. Let's  take a look at one of these roles, `AWSServiceRoleForSupport`. 
   
Run the following: 
`$ aws iam get-role --role-name AWSServiceRoleForSupport`

There is some metadata here around the name, ID, path, etc. The important fileds to note here are:

`ARN`: This is the fully qualified path to reference this specific role in this specific AWS account. ARNs are used in various places and every resource in AWS uses this format.

`AssumeRolePolicyDocument`: This section lays out the rules for who can use this role. This will be covered in more detail in the STS module, just note the field for now.


4. Now lets look at Policies. Just like Roles, AWS creates a number of Policies when the Account is created. 
Run the following to get a list of all Policy names:
```
$ aws iam list-policies | jq -r '.Policies[].PolicyName'
```

Let's take a look at one of the Policies to see its contents.

5. We want to take a look at `ElasticLoadBalancingReadOnly`. Let's follow the pattern from step's `get-roles` command to get the policy:

```
$  aws iam get-policy --policy-name ElasticLoadBalancingReadOnly

usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
To see help text, you can run:

  aws help
  aws <command> help
  aws <command> <subcommand> help

aws: error: the following arguments are required: --policy-arn

```

Looks like `--policy-name` doesn't work for an option, and we need to instead use `--policy-arn`. Let's look at how to build a policy ARN.

First, `arn:aws:iam::` is needed for any IAM related resource. Replace `iam` with the related service when building ARNs for resources outside of IAM.

Since this is a policy created by AWS, the next portion of the ARN is `aws:`. Let's see it all together so far:
`arn:aws:iam::aws:`. If this was a policy created by a user, that `aws:` would be replaced with the account ID, for example `"arn:aws:iam::335462696174:` for a policy created by a user in the account `335462696174:`.

Now that we have the service (IAM) and the right "scope" for the policy (AWS), we need to specify the specific resource we're trying to access. In this case, it's `policy/ElasticLoadBalancingReadOnly`, the type we're trying to get (policy) and the name of that policy. So all together, the ARN is 
```
arn:aws:iam::aws:policy/ElasticLoadBalancingReadOnly
```

Now let's try the command using this ARN
```
aws iam get-policy --policy-arn arn:aws:iam::aws:policy/ElasticLoadBalancingReadOnly
{
    "Policy": {
        "PolicyName": "ElasticLoadBalancingReadOnly",
        "PolicyId": "ANPAJMO7B7SNFLQ6HH736",
        "Arn": "arn:aws:iam::aws:policy/ElasticLoadBalancingReadOnly",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "Description": "Provides read only access to Amazon ElasticLoadBalancing and dependent services",
        "CreateDate": "2018-09-20T20:17:09+00:00",
        "UpdateDate": "2018-09-20T20:17:09+00:00",
        "Tags": []
    }
}
```

6. We were able to get information about the policy, but we aren't seeing what permissions this policy actually contains. Maybe we should look at [the documentation] (https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/get-policy.html) about this command to see what we're missing. 

```
This operation returns metadata about the policy. To retrieve the actual policy document for a specific version of the policy, use GetPolicyVersion .
```

Okay, so this command only returns metadata, and to actually get the permissions specified (in the "policy document"), we need to use GetPolicyVersion. Looking at the output from `get-policy`, there is a version ID!
```
"DefaultVersionId": "v1",
```

So let's try the command:
```
$ aws iam get-policy-version --policy-arn arn:aws:iam::aws:policy/ElasticLoadBalancingReadOnly --version-id v1


{
    "PolicyVersion": {
        "Document": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": "elasticloadbalancing:Describe*",
                    "Resource": "*"
                },
                {
                    "Effect": "Allow",
                    "Action": [
                        "ec2:DescribeInstances",
                        "ec2:DescribeClassicLinkInstances",
                        "ec2:DescribeSecurityGroups"
                    ],
                    "Resource": "*"
                }
            ]
        },
        "VersionId": "v1",
        "IsDefaultVersion": true,
        "CreateDate": "2018-09-20T20:17:09+00:00"
    }
}
```

There's some metadata here, bu the imporatnt section is the "Statements" field. This policy allows some Describe calls on any eleasticloadbalancer or ec2 resources to whoever has this policy.

