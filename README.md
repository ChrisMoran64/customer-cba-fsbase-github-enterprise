# GitHub Enterprise

https://enterprise.github.com/aws

Scripts and tools to automate GHE.

Based off [the official AWS Quickstart](https://github.com/aws-quickstart/quickstart-github-enterprise).

## Deployment Steps

### Build

N/A - Uses a pre-built, official AMI provided by the vendor.

### Test

TBC

### Deploy

1. Copy the GitHub License (`.ghl`) file to a (pre-existing) S3 bucket.
1. Create a `parameters.json` file with appropriate values (use the included `parameters.json-example` file as a starting point).
1. Create the application stack:
  ```
  aws cloudformation create-stack --region ap-southeast-2 \
    --template-body file://ghe.yaml \
    --parameters file://parameters.json \
    --capabilities CAPABILITY_IAM \
    --stack-name ghe-example-1
  ```
1. Check the stack outputs for the application's URL:
  ```
  aws cloudformation describe-stacks --query 'Stacks[0].Outputs[?OutputKey==`GHEURL`].OutputValue' \
    --output text \
    --stack-name ghe-example-1 
  ```

## Backup & Restore

GitHub provides a [script repo](https://github.com/github/backup-utils) with tools to remotely backup/restore a GHE installation.

### Backup

1. Clone the [backup-utils](https://github.com/github/backup-utils) repo.
1. Create a `backup.config` file (from the provided `backup.config-example` in the repo).
1. Edit the config file to include the relevant server information for the `GHE_HOSTNAME`.
1. Kick off the backup:
  ```
  ./bin/ghe-backup
  ```

### Restore

1. Deploy a new GHE stack.
1. Log in to the stack using the created admin user.
1. Put the new GHE instance [in to maintenance mode](https://help.github.com/enterprise/2.10/admin/guides/installation/maintenance-mode/).
1. Clone the [backup-utils](https://github.com/github/backup-utils) repo.
1. Create a `backup.config` file (from the provided `backup.config-example` in the repo).
1. Edit the config file to include the relevant server information for the `GHE_RESTORE_HOST`.
1. Restore the last backup to the new instance using the `ghe-restore` command:
  ```
  ./bin/ghe-resetore <target IP>
  ```
  See the `backup-utils` repo for more detailed restoration commands.

## Updating

The recommended upgrade process involves a _manual_ process [according to this official documentation](https://help.github.com/enterprise/2.9/admin/guides/installation/upgrading-github-enterprise/).
