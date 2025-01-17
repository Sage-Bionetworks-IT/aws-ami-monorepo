# aws-ami-monorepo
Project to generate AMIs using the AWS image builder

## Workflow
The workflow to generate AMIs is done using pull requests.
Request using PRs provide history, gating, reviewing and an approval
process.

### Add Images
Step by step instructions to create a new image:

The first step is to create the definition template:
1. Create an Image Builder definition (cloudformation) template and add it to
the [Sage-Bionetworks/aws-infra](https://github.com/Sage-Bionetworks/aws-infra)
repo in the `templates/ImageBuilder` directory.
2. Create a PR with this change to aws-infra repo
3. Review, approve and Merge the PR
4. Tag the repo with a [version number](https://github.com/Sage-Bionetworks/aws-infra/tags)

Next step is to use the definition template to provision an AMI:
1. In this repo, add a new Sceptre config in `config/prod` directory referencing
a versioned (or tagged) instance of the cloudformation template.  Set the
the `ImageVersion` to the same number as the aws-infra repo tag (i.e. 0.9.2 without the `v`).
It must match a [semantic version](https://semver.org/) number.
2. Create a PR to merge the new config into the `main` branch.
3. Once the PR is merged, the cloudformation template will be deploy to the
AWS org-sagebase-imagecentral account which will in turn trigger a build
of the image which will generate an AMI.
4. The AMI will be shared to all accounts in our AWS organization which
means that it is searchable from any of our AWS accounts using either the
AWS console EC2 Instances->AMIs or Image Builder->Images pages. The AMI is
also searchable using the AWS CLI
[describe-images command](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html).


### Updating Images
To update an image the we must first update aws-infra then this repo.

Update the cloudformation template in aws-infra:
1. Create a PR to update the ImageBuilder cloudformation template in the aws-infra repo
2. Review, approve and Merge the PR
3. Tag the repo with a version number

Update the Sceptre config in this repo:
1. Create a PR to update the template `url` reference and `ImageVersion` number
in the Sceptre config file.
2. Review, approve and Merge the PR
3. Once merged the cloudformation template will be deploy which will trigger
an update to the AMI.  AMIs are immutable therefore AWS will create a new AMI
on every change.  The updated AMIs will retain the same name, only the version
number is updated.

__Note__: An update to the image definition (or cloudformation template) requires
an `ImageVersion` update otherwise cloudformation may fail with a message similar to
`The following resource XXXXX already exists..`.

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

__NOTE__: Step #2 above can also be done using the `sceptre delete` command or the AWS CLI.

### Testing Images
To test an image we recommend that you manually provision an EC2 instance from the
AMI image then connect to it using the [AWS session manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)
or [SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connect-linux-inst-ssh.html).
Validate that the image meets the specifications defined in the cloudformation template and
that  image is generally running as expected.


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
