# :building_construction: Infrastructure as Code

Setup AWS infrastructure with `CloudFormation` templates.

This repository helps you set up networking resources such as `VPC (Virtual Private Cloud)`, `Internet Gateway`, `Route Tables` and `Routes`.

We will use `AWS CloudFormation` for infrastructure setup and tear-down.

> NOTE: Provide unique names to the resources (wherever supported).  You should be able to create multiple networks in the same account.

## :hammer_and_wrench: AWS IAM Setup

Use the following instructions to set up `dev`, `prod` and `root` profiles for resource creation using `AWS CloudFormation`.

### :busts_in_silhouette: IAM User Groups

- Sign in to your AWS `root` account console.
- Navigate into the IAM console.
- Create a user group named `csye6225-ta` with `ReadOnlyAccess` privileges.
- Follow the above two steps for `dev` and `prod` accounts.

### :bust_in_silhouette: IAM Users

- Sign in to your AWS `root` account console.
- Navigate into the IAM console.
- Create a user by providing the `username`.
- Add the `username` user to the user group `csye6225-ta` created above.
- Do not configure credentials for the users. Leave the default setting _"Autogenerated password"_ checked and copy the generated password. **AWS does not email autogenerated passwords. You need to manually send the email with the password**.
- Provide appropriate tag(s), they're highly recommended.

## :pager: AWS Command Line Interface (v2)

- Install and configure AWS Command Line Interface (CLI) on your development machine (laptop). See [Install the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) for detailed instructions to use AWS CLI with Windows, MacOS or Linux.
- Below are the steps to download and use the AWS CLI on MacOS:
- Download the file using the `curl` command:

```shell
# On macOS only
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
```

- Run the `macOS installer` to install AWS CLI:

```shell
# On macOS only
sudo installer -pkg ./AWSCLIV2.pkg -target /
```

- Verify that `zsh` can find and run `aws` command using the following commands:

```shell
which aws
#/usr/local/bin/aws
aws --version
#aws-cli/2.8.2 Python/3.9.11 Darwin/21.6.0 exe/x86_64 prompt/off
```

> NOTE: Alternatively, you can use the homebrew to install AWS CLI v2 on your Mac. See detailed instructions [here](https://formulae.brew.sh/formula/awscli).

- Create a `CLI` group in your `dev` and `prod` root accounts, on the AWS Console.
- Provide the `Administrator Access` policy to this group.
- Add the `dev-cli` and `prod-cli` users to their respective user groups.
- In the terminal, create `dev` user profile for your dev AWS account and `prod` user profile for your production AWS account. **Do not set up a `default` profile**.
- Both `dev` and `prod` AWS CLI profiles should be set to use the `us-east-1` region or the region closest to you.
- To create a profile, use the set of following command:

```shell
aws configure --profile <profile-name>
```

- The above command will ask you to fill out the following:
  - `AWS Access Key ID`
  - `AWS Secret Access Key`
  - `Region`
  - `Output`

- To change the region on any profile, use the following command:

```shell
# change the region
aws configure set region <region-name> --profile dev
```

```shell
# you can omit --profile dev is you have env variables set (see below)
aws configure set region <region-name>
```

- To use a particular profile, use the command:

```shell
# For prod profile
export AWS_PROFILE=prod
```

```shell
# For dev profile
export AWS_PROFILE=dev
```

- To stop using a profile, use the following command:

```shell
# To stop using a profile
export AWS_PROFILE=
```

## :cloud: [AWS CloudFormation](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html)

Configure the networking infrastructure setup using AWS Cloudformation:

- Create a CloudFormation template `csye6225-infra.json` or `csye6225-infra.yml` that can be used to set up required networking resources.
- Do not hardcode values for your VPCs and its networking resources.

### :construction: AWS Networking Setup

- Create a [Virtual Private Cloud](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)(VPC).
- Create [subnets](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#AddaSubnet) in your VPC. You must create `3` subnets, each in a different availability zone in the same region in the same VPC.
- Create an [Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) resource and attach the Internet Gateway to the VPC.
- Create a public [route table](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html). Attach all subnets created to the route table.
- Create a public route in the public route table created above with the destination CIDR block `0.0.0.0/0` and internet gateway created above as the target.

### :rocket: Create VPCs using CloudFormation

- Validate the CloudFormation template using the following command:

```shell
aws cloudformation validate-template --template-body file://<path-to-template-file>.yaml
```

- To create the stack, run the following command:

```shell
aws cloudformation create-stack --stack-name <stack-name> --template-body file://<path-to-template-file>.yaml
```

- To update the stack, run the following command:

```shell
aws cloudformation update-stack --stack-name <stack-name> --template-body file://<path-to-template-file>.yml
```

- To delete the stack, run the following command:

```shell
aws cloudformation delete-stack --stack-name <stack-name>
```

## :desktop_computer: Stack and VPC details

- To list all the stacks in the current `AWS_PROFILE`, use the following command:

```shell
aws cloudformation list-stacks --output table
```

- To view the details of the stack created, run the following command:

```shell
# displays the result in a table format
aws cloudformation describe-stacks --stack-name <stack-name> --output table
```

- To view the details of VPCs created, run the following command:

```shell
# displays the result in a table format
aws ec2 describe-vpcs --output table
```

- To get the AZs(Availability Zones) of a region, use the following command:

```shell
aws ec2 describe-availability-zones [--region <region-name>] --output table
```

## :arrow_double_up: Custom Amazon Machine Image (AMI)

To create a stack with custom AMI, replace the AMI default value under the `AMI` parameter with the custom AMI id that is created using packer:

```yaml
parameters:
  AMI:
    Type: String
    Default: "<your-ami-id>"
    Description: "The custom AMI built using Packer"
```

## :ninja: Author

[Siddharth Rawat](mailto:rawat.sid@northeastern.edu)

## :scroll: License

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)
