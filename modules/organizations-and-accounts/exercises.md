# CLI Exercises

## Introduction

In this section there are several challenges laid out. They should all be
executed using the AWS cli only. If you're stuck, try looking for the right command
and its options [on the cli reference page](https://docs.aws.amazon.com/cli/latest/reference/organizations/index.html#available-commands).

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
```
Key         | Value
--------------------------
"Name"      | "$kerberosID"
"Challenge" | "2"
```

## Challenge 3
Create a new Organizational Unit with ou-rs3h-1pslrete as the parent 
and use the following name: ${kerberosID}-challenge-3.

## Challenge 4
Move the AWS account from #1 into the OU you created from #3.

## Challenge 5
Add a tag to your OU from #3:
```
Key         | Value
--------------------------
"Name"      | "$kerberosID"
"Created"   | "$date"
"Challenge" | "5"
```

