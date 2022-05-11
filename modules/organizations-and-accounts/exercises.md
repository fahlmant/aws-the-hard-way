# CLI Exercises

Pre-requisite: Setup the osd-staging-2 profile in your ~/.aws/credentials file. Run `aws sts get-caller-identity --profile osd-staging-2`

The output should match:
```
{
    "UserId": "AIDAIFSL3RQLR6XXDVO42",
    "Account": "811685182089",
    "Arn": "arn:aws:iam::811685182089:user/<kerberosID>"
}
```

## Challenge 1
Find an AWS account in the osd-staging-2 Organization that has no tags. 

## Challenge 2
Using the AWS account from #1, add two tags:
Key    | Value
--------------
"Name"      | "$kerberosID"
"Challenge" | "2"

## Challenge 3
Create a new Organizational Unit in the osd-staging-2 Organization
with the following name: $kerberosID-challenge-3

## Challenge 4
Move the new OU from #3 into the following OU:

## Challenge 5
Move the AWS account from #1 into the OU from #3