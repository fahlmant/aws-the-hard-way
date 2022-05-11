# AWS Organizations and Accounts

## Intro
AWS Organization is a way to manage multiple related AWS Accounts. 

Please read the following AWS docs:
[Organizations Introduction](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_introduction.html)
[Organizations Terminology and Concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)

## SREP Organizations and Accounts

SREP mantains 4 AWS Organizations, each with a different purpose. Remember that an Organization name corresponds to a single account used for management and administration

1. osd-staging-1 - 277304166082
   Used for AWS Account Operator Accounts for the stage and integration environments.
2. osd-staging-2 - 811685182089
   Used for individual SREP developer accounts, as well as Scale Testing cluster creation events in OCM
3. rhcontrol - 922711891673
   Used for AWS Account Operator Accounts for the production environment, v3 Accounts, some external team accounts, team infrastructure
4. fedramp
   Used to start the flux capacitor

Generally, no commands or work should be done in these four accounts directly unless approved by SREP management.
Please make sure no clusters are created in these accounts.

Most development and testing work for an individual SREP should be done in the developer account allocated to them in the osd-staging-2 organization.

AWS Account Operator provisions new AWS accounts for OSD non-CCS clusters to be installed into. It uses the osd-staging-1 and
rhcontrol to create accounts under. It organizes those accounts into various [Organizational Units (OUs)](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html).





