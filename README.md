*Versions < 3 of ELBAS are no longer being maintained. I will only be maintaining the current feature-set which relies on Launch Templates and AWS SDK v3.*

# Capistrano ELBAS (Elastic Load Balancer & AutoScaling)

[![Gem Version](https://badge.fury.io/rb/elbas.svg)](https://badge.fury.io/rb/elbas)

ELBAS was written to ease the deployment of Rails applications to AWS AutoScale
groups. During your Capistrano deployment, ELBAS will:

- Deploy your code to each running instance connected to a given AutoScale group
- After deployment, create an AMI from one of the running instances
- Update the AutoScale group's launch template with the AMI ID
- Delete any outdated AMIs created by previous ELBAS deployments

## Installation

Add to Gemfile, then `bundle`:

`gem 'elbas'`

Add to Capfile:

`require 'elbas/capistrano'`

## Configuration

Setup AWS credentials:

```ruby
set :aws_access_key, ENV['AWS_ACCESS_KEY_ID']
set :aws_secret_key, ENV['AWS_SECRET_ACCESS_KEY']
set :aws_region,     ENV['AWS_REGION']
```

## Usage

Instead of using Capistrano's `server` method, use `autoscale` instead in
`deploy/<environment>.rb` (replace <environment> with your environment). Provide
the name of your AutoScale group instead of a hostname:

```ruby
autoscale 'my-autoscale-group', user: 'apps', roles: [:app, :web, :db]
```

Run `cap production deploy`.

**As of version 3, your AWS setup must use launch templates as opposed to launch
configurations.** This allows ELBAS to simply create a new launch template version
with the new AMI ID after a deployment. It no longer needs to update your
AutoScale group or mess around with network settings, instance sizes, etc., as
that information is all contained within the launch template. Failure to use a
launch template will result in a `Elbas::Errors::NoLaunchTemplate` error.
