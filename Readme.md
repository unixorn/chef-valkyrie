[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Code Climate](https://codeclimate.com/github/unixorn/chef-valkyrie/badges/gpa.svg)](https://codeclimate.com/github/unixorn/chef-valkyrie)
[![Issue Count](https://codeclimate.com/github/unixorn/chef-valkyrie/badges/issue_count.svg)](https://codeclimate.com/github/unixorn/chef-valkyrie)

# chef-valkyrie

## Motivation

Cleaning up nodes that were autoscale group members or were terminated abnormally in EC2 so they don't linger in your Chef server's list (where you get to pay for the zombie nodes) is an annoying ongoing housekeeping chore if you have instances in EC2.

Named after the Norse choosers of the slain, `chef-valkyrie` is a tool that will connect to your Chef server, check all of your nodes to see if they're in a specified AWS account, then delete any stale nodes that are in that AWS account.

## How does chef-valkyrie decide an instance is stale and should be purged?

`chef-valkyrie` assumes any instances that can't be found at all were  terminated long enough ago that AWS has cleaned them out of the database and are eligible to be purged.

Instances that are found, but report a status of _terminated_ will also be purged.

## Installation

### Common tasks

1. Create a utility account on your Chef server and download the client pemfile from it.
2. Create an IAM account with permission to read instance informationfrom EC2. You can either do this as a standard account and specify `$AWS_ACCESS_KEY_ID` and `$AWS_SECRET_ACCESS_KEY` or just create a role and assign it to the instance you're going to run this on.
3. Create a working directory with a **.chef** subdirectory
4. Copy the pem file from step 1 into your new **.chef** directory
5. Add a **knife.rb** to your chef directory and configure it to use the pem file and utility account from step 2

### Manually

1. Check out this repository on the machine you're going to run `chef-valkyrie` on.
2. Copy the `chef-valkyrie` script wherever you store local administrative scripts.

### Packaging

You will need to have **thor** installed on your packaging machine (`gem install thor` if necessary)

1. Check out the repository
2. `thor bakery:rpm` will generate an RPM for you
3. Install the generated RPM with a configuration management system or `yum`.

### Finally, set it to run automatically

1. Create a wrapper script that cds to the working directory you created that contains a valid **.chef** directory with a **knife.rb** and pemfile, then runs `chef-valkyrie --aws-account-id YOUR_ACCOUNT_NUMBER` with the IAM credentials you generated.
2. Create a cron job to run your wrapper

## Usage

`chef-valkyrie --aws-account-id 123456789012`

**chef-valkyrie** supports `--log-level` and `--dry-run` options you can use during testing.

## ToDo

Make this a lambda function.
