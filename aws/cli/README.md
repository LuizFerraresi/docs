# Amazon Web Services CLI Login

How to configure credentials to work locally with AWS CLI, we will cover short and long
term credentials and also SSO Login.

## Requirements

- [AWS CLI](https://docs.aws.amazon.com/pt_br/cli/latest/userguide/getting-started-install.html)

## Disclaimer

- [Official Documentation](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)

- Always remember AWS CLI hierarchy for credentials when using the profiles, if you
exported environment variables they will prevail on ``~/.aws/config`` file.

- When executing a command, always use ``--region`` and ``--profile`` to select the 
correct account/role

## Config IAM User Long Term Credentials

The easiest way is throught environment variables export command:

```bash
export AWS_ACCESS_KEY_ID=
export AWS_SECRET_ACCESS_KEY=
export AWS_DEFAULT_REGION=
```

You can use the CLI and set the credentials manually:

```bash
aws configure
```

Or you can import from .csv credentials file

```bash
aws configure import --csv file://credentials.csv
```

The auth and configuration it will be set as ``default`` profile

## Config IAM User Short Term Credentials

You can access Your multi account link, open the avaliable roles for the account and
click on cli were will be shown access credentials but not the long term ones, it will
have ``SESSION TOKEN``, an it will expire after a period and the access will be
blocked, just export the varibales show on screen on your bash.

```bash
export AWS_ACCESS_KEY_ID=
export AWS_SECRET_ACCESS_KEY=
export AWS_SESSION_TOKEN=
export AWS_DEFAULT_REGION=
```

## SSO

Here you can create one SSO Session and reuse across multiple profiles, so you can let
many accounts configured at once in your CLI.

```bash
aws configure sso
```

After running the config command you need to fullfill the folowing infos:

```bash
SSO session name (Recommended):
SSO start URL [None]:
SSO region [None]:
SSO registration scopes [sso:account:access]:
```

* If you are having trouble to make SSO works, use ``--debug`` to see all actions and
it's logs on the terminal.

After completing the SSO config, you will be redirected to your brownser to an AWS page.

Select ``Allow`` and then ``Allow Access``.

After accepting this two sections a ``Request Approve`` should be shown in your brownser.

If you are using AWS Organizations, all accounts should be shown in a drop down at
your prompt, select the account you desire.

After that a drop down with avaliable roles will be shown, select the role you desire.

Now just complete your local config with

```bash
CLI default client Region [None]: <AWS REGION>
CLI default output format [None]: json
CLI profile name [RoleName-AccountId]: <PROFILE ALIAS>
```

### Add Accounts to SSO

To add acounts to your SSO, you can reuse your session, to do that, just write you
already configured session name on ``SSO session name``, a box on the bottom of your
prompt should apper with your already configured session infos, press TAB to fullfill
the session infosand then you can add another account.

### Config SSO Login with Config File

You can edit the existing file

```bash
nano ~/.aws/config
```

Or you can overwrite the current file trought command line

```bash
cat > ~/.aws/config <<EOF
    < CONFIG FILE >
EOF
```

This is an example of how your config file will look like

```ini
# /.aws/config

[default]
sso_session = my-sso
sso_account_id = <AWS ACCOUNT ID>
sso_role_name = readOnly
region = < AWS REGION>
output = json

[profile user-one]
sso_session = my-sso
sso_account_id = <AWS ACCOUNT ID>
sso_role_name = readOnly
region = < AWS REGION >
output = json

[sso-session my-sso]
sso_region = <AWS REGION>
sso_start_url = https://my-sso-portal.awsapps.com/start
sso_registration_scopes = sso:account:access
```

## Login on AWS SSO

```bash
aws sso login --profile < PROFILE NAME >
```

## Switching Profiles

```bash
export AWS_PROFILE=
```

* if you use custom prompt, propably you will need this var for custom appearence.

You can clear the credentials cache by running:

```bash
rm -r ~/.aws/cli/cache
```

### Add Kubernetes Cluster to Your Local Context

```bash
aws eks \
    --region < REGION NAME > \
    --profile < PROFILE NAME > \
    update-kubeconfig --name < CLUSTER NAME >
```
