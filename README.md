# aws-ami-monorepo
Project to generate AMIs using the AWS image builder

## Workflow
The workflow to generate AMIs is done using pull requests.
Request using PRs provide history, gating, reviewing and an approval
process.

The image builder cloudformation templates should be added to
[Sage-Bionetworks/aws-infra](https://github.com/Sage-Bionetworks/aws-infra)
repo in the templates/ImageBuilder directory.  Sceptre configs will then
reference a versioned (or tagged) instance of the cloudformation template
to deploy to the AWS org-sagebase-imagecentral account which will then
trigger a build of the AMI.

## Contributions
Contributions are welcome.

Requirements:
* Install [pre-commit](https://pre-commit.com/#install) app
* Clone this repo
* Run `pre-commit install` to install the git hook.

## Testing
As a pre-deployment step we syntatically validate our sceptre and
cloudformation yaml files with [pre-commit](https://pre-commit.com).

Please install pre-commit, once installed the file validations will
automatically run on every commit.  Alternatively you can manually
execute the validations by running `pre-commit run --all-files`.

### Stack & config file names
The value of a stack's `stack_name` parameter must match the config's file
name.  Stack names can contain only alphanumeric characters (case-sensitive)
and hyphens. They must start with an alphabetic character and can't be longer
than 128 characters.

## Deployments
We use [sceptre](https://sceptre.github.io/) and [cloudformation](https://aws.amazon.com/cloudformation/)
to deploy resources onto an AWS account.

## Continuous Integration
We have configured github actions to deploy CF template which
in turn will build AMI images using the AWS image builder

## Issues
* https://sagebionetworks.jira.com/projects/IT

## Builds
* https://travis-ci.org/Sage-Bionetworks/scicomp-provisioner
