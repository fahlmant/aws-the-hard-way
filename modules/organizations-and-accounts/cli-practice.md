# Common CLI actions for AWS Organizations and Accounts

All Organizations API calls can be seen [on the cli reference page](https://docs.aws.amazon.com/cli/latest/reference/organizations/index.html#available-commands).



1. Pre-requisite: Setup the osd-staging-2 profile in your ~/.aws/credentials file. Run `aws sts get-caller-identity --profile osd-staging-2`

The output should match:
```
{
    "UserId": "AIDAIFSL3RQLR6XXDVO42",
    "Account": "811685182089",
    "Arn": "arn:aws:iam::811685182089:user/<kerberosID>"
}
```

1. Run ` aws organizations list-accounts --profile osd-staging-2`
This will list all accounts that are within the organization osd-staging-2 (i.e. root account 811685182089)
NOTE: This call may take a long time to return, or show an error related to rate limiting. Wait a few seconds, then try again.

The output should look something like this
```
{
    "Accounts": [
        {
            "Id": "514758378285",
            "Arn": "arn:aws:organizations::811685182089:account/o-hl29u3vrs0/514758378285",
            "Email": "osd-creds-mgmt+7bs8fd@redhat.com",
            "Name": "osd-creds-mgmt-7bs8fd",
            "Status": "ACTIVE",
            "JoinedMethod": "CREATED",
            "JoinedTimestamp": "2021-02-17T19:55:05.380000-05:00"
        },
        {
            "Id": "113007233969",
            "Arn": "arn:aws:organizations::811685182089:account/o-hl29u3vrs0/113007233969",
            "Email": "osd-creds-mgmt+s89pzg@redhat.com",
            "Name": "osd-creds-mgmt-s89pzg",
            "Status": "ACTIVE",
            "JoinedMethod": "CREATED",
            "JoinedTimestamp": "2021-02-17T21:46:14.711000-05:00"
        },
    ]
}

```

This shows a JSON array of Accounts, which contains the 
account ID, ARN, name and some other metadata.

1. Let's run the same command, but apply some JQ filtering to just the the list of account IDs. 

Run `aws organizations list-accounts --profile osd-staging-2 | jq -r '.Accounts[].Id'`

This query selects the Id field from every item in the Accounts array. The output should look like this:
```
...
499662239765
816809686451
532796732541
727239477276
...
```

osd-staging-2 has thousands of accounts, so it will be a long list.

3. There is a root OU in every organization. Let's find the root for our org:

`aws organizations list-roots --profile osd-staging-2`

Copy the value in the Id field.

4. Run `aws organizations list-organizational-units-for-parent --profile osd-staging-2 --parent-id $ROOT`, replacing $ROOT with the root Id you copied from the previous step. Self-test: What does this command return?

5. Let's try and simulate an osdctl command.

`osdctl account mgmt list -u tfahman -p osd-staging-2` shows accounts that are "assigned" to a specific user in the osd-staging-2 org

```
  Username: tfahlman
  Accounts: [248207035884 335462696174]
```

This command looks for a Tag on accounts in the organization provided. Lets look at the tags directly for one of those accounts:

```
$ aws organizations list-tags-for-resource --profile osd-staging-2 --resource-id 335462696174
{
    "Tags": [
        {
            "Key": "owner",
            "Value": "tfahlman"
        },
        {
            "Key": "claimed",
            "Value": "true"
        }
    ]
}
```

Using the AWS cli, we can see the tag with the Key owner and the Value tfahlman is what osdctl uses.

Now that we've practiced using the AWS cli a bit, let's try some [exercises](exercises.md)