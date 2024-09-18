# [terracognita](https://github.com/cycloidio/terracognita)

Generate Terraform definition files from your Cloud Provider.

## Required

- Brew
- AWS IAM Credentials

## Installing

```bash
brew install terracognita
```

## Authentication

To Terracognita connect to your provider you need to export your AWS Credentials to
your prompt.

## How it works

Terracognita will scan your aws account or apecified resource and will map it to a local
`.tf` files and also one `.tfstate` file, this is how your resources get mapped from
your cloud provider to your local environemnt.

To update your terraform project with the new resources, first add the resources to your
project path if you have not exported it in the correct path, add the state file on the
path too. When you run `terraform apply` it will only revalidade the state files and
sync the resources into your project.

> [!TIP]
> Alvays validate the changes with ``terrafrom plan`` before applying the changes

> [!TIP]
> The name of terraform resources created by terracognita cannot be changed, because it
> ensures that terraform just map the changes and don't create a new resource

## Import Resources

```bash
terracognita aws \
  --hcl tc-extract \                     # folder to save resouces definitions
  --tfstate tc-extract.tf \              # tf state file name
  --aws-default-region <AWS REGION> \
  --hcl-provider-block false \           # include tf provider block
  --verbose                              # enable verbose logging
```

It's possible to include/specify a resource or a list of resources with ``-i/--include``
and you can exclude resources with ``-e/--exclude``.

Or you can import only one specific item from an specific resource by using it's ID and
the param ``--target``.

```bash
  --include <TERRAFORM RESOURCE NAME> \          # speficy resource to be imported
  --target <TERRAFORM RESOURCE NAME.RESOURCE ID> # speficy resource ID or NAME to be imported
```

The ``--tfstate`` argument is recomended to be with the module or resources you want to import,

## Import Resource as Module

```bash
terracognita aws \
  --tfstate tc-extract.tf \                     # tf state file name
  --aws-default-region <AWS REGION> \
  --include <TERRAFORM RESOURCE NAME> \         # speficy resource to be imported
  --module <PATH TO SAVE MODULE> \              # specify path to save module files
  --module-variables < VARIABLES FILE PATH > \  # specify path to variable file JSON or YAML
  --hcl-provider-block false
  --verbose
```

The ``--module-varibales`` file is used to create only the variables declared on it, the file can
be ``.yaml`` or ``.json``, in the file you will specify the resourtce name as terraform resource
documentation and the variable values to be created should be a list inside each resource.
Check examples on terracognita documentation.

If you are using modules you don't need to define ``--hcl``, the resources will be imported to
module folder specified on ``--module``.
