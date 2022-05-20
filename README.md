# WWT PoC

## Blueprints

* `aws-nginx.yaml` - Instantiates one or more instances on AWS
* `gcp-nginx.yaml` - Instantiates one or more instances on GCP
* `eaas.yaml` - Uses service composition to deploy one or more app servers, configured via Ansible, on AWS or GCP
* `blueprint.yaml` - Placed in [Cloudify-PS/wwt-tf-templates](https://github.com/Cloudify-PS/wwt-tf-templates/blob/master/blueprint.yaml) repo. Uses service composition to deploy GitHub terraform repository.

# Prerequisites

The blueprints assume that you have the following secrets defined:

* `aws_access_key_id` - AWS access key ID
* `aws_secret_access_key` - AWS secret access key
* `gcp_credentials_json` - Credentials file for GCP
* `cloudify_host` - Cloudify Manager address used by GitHub Actions 
* `cloudify_user` - Cloudify Manager username
* `cloudify_password` - Cloudify Manager password
* `github_token` - [GitHub Access Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) with delete_repo & repo scopes.
* `runner_host` - FQDN/IP of [GitHub self-hosted runner](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners), eg. `10.255.66.77`
* `runner_vm_user` - Runner's SSH user, eg. `labuser`
* `runner_root_user` - Runner's SSH root, eg. `root`
* `runner_key_private` - Runner's SSH private key

Please make sure that your GitHub self-hosted runner satisfies all [requirements](https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners#requirements-for-self-hosted-runner-machines), and it has Docker installed.
## Usage

First, upload the EaaS secrets file in [secrets/eaas_params_{dev/qa/prod}.json](./secrets/eaas_params_dev.json).

Then, upload the three necessary blueprints: `aws-nginx.yaml`, `gcp-nginx.yaml`, `eaas.yaml` and [terraform repository blueprint](https://github.com/Cloudify-PS/wwt-tf-templates/blob/master/blueprint.yaml).

There is a helper script that you can run to handle this. The script takes one parameter, valid values are:  `dev`/`prod`/`qa`. Cloudify tenants can be changed using [cfy profile](https://docs.cloudify.co/latest/cli/maint_cli/profiles/) from Cloudify CLI.

```
❯ ./upload_blueprints.sh dev
Secret `eaas_params` created
...
Publishing blueprint archive https://github.com/Cloudify-PS/wwt-tf-templates/archive/refs/heads/master.zip...
Blueprint `terraform-repository` upload started.
2022-01-13 21:40:41.845  CFY <None> Starting 'upload_blueprint' workflow execution
2022-01-13 21:40:42.522  LOG <None> INFO: Blueprint archive uploaded. Extracting...
2022-01-13 21:40:42.642  LOG <None> INFO: Blueprint archive extracted. Parsing...
2022-01-13 21:40:45.223  LOG <None> INFO: Blueprint parsed. Updating DB with blueprint plan.
2022-01-13 21:40:45.379  CFY <None> 'upload_blueprint' workflow execution succeeded
Blueprint uploaded. The blueprint's id is terraform-repository
Uploading blueprint aws_blueprint.zip...
 aws_blueprint.zip |###################################################| 100.0%
Blueprint `aws-nginx` upload started.
2022-01-13 21:40:48.593  CFY <None> Starting 'upload_blueprint' workflow execution
2022-01-13 21:40:48.680  LOG <None> INFO: Blueprint archive uploaded. Extracting...
2022-01-13 21:40:48.787  LOG <None> INFO: Blueprint archive extracted. Parsing...
2022-01-13 21:40:52.645  LOG <None> INFO: Blueprint parsed. Updating DB with blueprint plan.
2022-01-13 21:40:52.870  CFY <None> 'upload_blueprint' workflow execution succeeded
Blueprint uploaded. The blueprint's id is aws-nginx
...
Uploading blueprint gcp_blueprint.zip...
 gcp_blueprint.zip |###################################################| 100.0%
Blueprint `gcp-nginx` upload started.
2022-01-13 21:40:58.590  CFY <None> Starting 'upload_blueprint' workflow execution
2022-01-13 21:40:58.681  LOG <None> INFO: Blueprint archive uploaded. Extracting...
2022-01-13 21:40:58.777  LOG <None> INFO: Blueprint archive extracted. Parsing...
2022-01-13 21:41:04.008  LOG <None> INFO: Blueprint parsed. Updating DB with blueprint plan.
2022-01-13 21:41:04.183  CFY <None> 'upload_blueprint' workflow execution succeeded
Blueprint uploaded. The blueprint's id is gcp-nginx
~/src ~/src/wwt-poc
...
Uploading blueprint eaas_nginx_blueprint.zip...
 eaas_nginx_bluepr... |################################################| 100.0%
Blueprint `nginx-eaas` upload started.
2022-01-13 21:41:08.506  CFY <None> Starting 'upload_blueprint' workflow execution
2022-01-13 21:41:08.606  LOG <None> INFO: Blueprint archive uploaded. Extracting...
2022-01-13 21:41:08.726  LOG <None> INFO: Blueprint archive extracted. Parsing...
2022-01-13 21:41:12.210  LOG <None> INFO: Blueprint parsed. Updating DB with blueprint plan.
2022-01-13 21:41:12.410  CFY <None> 'upload_blueprint' workflow execution succeeded
Blueprint uploaded. The blueprint's id is nginx-eaas
~/src/wwt-poc
```

You can now deploy the main `nginx-eaas` blueprint.