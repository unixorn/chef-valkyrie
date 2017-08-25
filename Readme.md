# chef-valkyrie

## Motivation

Cleaning up nodes that were autoscale group members or were terminated abnormally in EC2 so they don't linger in your Chef server's list (where you get to pay for the zombie nodes) is an annoying ongoing housekeeping chore if you have instances in EC2.

Named after the Norse choosers of the slain, `chef-valkyrie` is a tool that will connect to your Chef server, check all of your nodes to see if they're in a specified AWS account, then delete any stale nodes that are in that AWS account.

## How does chef-valkyrie decide an instance is stale and should be purged?

`chef-valkyrie` assumes any instances that can't be found at all were  terminated long enough ago that AWS has cleaned them out of the database and are eligible to be purged.

Instances that are found, but report a status of _terminated_ will also be purged.

## Installation

1. Check out this repo
2. Create a utility account on your Chef server and download the client pemfile from it.
3. Create a **.chef** directory
4. Copy the pem file from step 2 to the **.chef** directory
5. Add a **knife.rb** to your chef directory and configure it to use the pem file and utility account from step 2
6. Create an IAM account with permission to read instance information from EC2.
7. Set up a cron job to run `chef-valkyrie --aws-account-id YOUR_ACCOUNT_NUMBER` with the IAM credentials from step 6

## ToDo

Make this a lambda function.
