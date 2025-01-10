# aws-ami-monorepo
Project to generate AMIs using the AWS image builder

## Workflow
The workflow to generate AMIs is done using pull requests.
Request using PRs provide history, gating, reviewing and an approval
process.

### Add Images
Step by step instructions to add a new image to the image builder:

1. Add image builder cloudformation templates should be added to
[Sage-Bionetworks/aws-infra](https://github.com/Sage-Bionetworks/aws-infra)
repo in the templates/ImageBuilder directory.
2. Add a new Sceptre config in `config/prod` directory referencing
a versioned (or tagged) instance of the cloudformation template.
3. Create a PR to merge the new config into the `main` branch.
4. Once the PR is merged, the image builder will be triggered to
to deploy to the AWS org-sagebase-imagecentral account which will then
trigger a build of the AMI.
5. The AMI will be shared to all accounts in our AWS organization which
means that it is searchable using the

### Removing Images
Important info when removing image builder resources and the generated
images.

1. Deleting a file from this repo will not automatically remove the cloudformation
stack or remove existing image builder resources.
2. To delete image builder resources you need to go into the AWS console -> cloudformation
then delete the cloudformation stack for it.  Note that deleting the cloudformation stack
does not delete the generated AMIs.
3. Typically the generated AMIs are immutable and should never be deleted however there are
situations where it is appropriate to delete AMIs, for example when testing AMIs.
To delete AMIs go into the AWS console EC2 -> Images -> AMIs, search for the AMIs
then delete (or de-register) them.

__NOTE__: Steps above can also be done using the `sceptre delete` command or the AWS CLI.

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
