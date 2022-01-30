# Terraform Plan - GitHub Action (terraform-azurerm-plan)

Connects to a remote Terraform backend in Azure, creates a terraform plan and uploads plan as a workflow artifact. (Additionally TFSEC IaC scanning can be enabled).  

See my [detailed tutorial](https://dev.to/pwd9000/multi-environment-azure-deployments-with-terraform-and-github-2450) for more usage details.  

**NOTE:** Can be used independently with Action: **[Pwd9000-ML/terraform-azurerm-apply](https://github.com/marketplace/actions/terraform-apply-for-azure)**.  

## Installation

```yaml
steps:
  - name: Dev TF Plan
    uses: Pwd9000-ML/terraform-azurerm-plan@v1.0.0
    with:
        path: "path-to-TFmodule"                 ## (Optional) Specify path TF module relevant to repo root. Default="."
        az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
        az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage acc 
        az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
        tf_key: "state-file-name"                ## (Required) AZ backend - Specifies name that will be given to terraform state file and plan artifact
        tf_vars_file: "tfvars-file-name"         ## (Required) Specifies Terraform TFVARS file name inside module path
        enable_TFSEC: true                       ## (Optional)  Enable TFSEC IaC scans (Private repo requires GitHub enterprise)
        arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
        arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
        arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
        arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
```

## Usage

Usage example of a terraform plan with apply.

```yaml
name: "TF-Deployment-01"
on:
  workflow_dispatch:
  pull_request:
    branches:
      - master
jobs:
  Plan_Dev:
    runs-on: ubuntu-latest
    environment: null #(Optional) If using GitHub Environments          
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Dev TF Plan
        uses: Pwd9000-ML/terraform-azurerm-plan@v1.0.0
        with:
          path: "path-to-TFmodule"                 ## (Optional) Specify path TF module relevant to repo root. Default="."
          az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
          az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage acc 
          az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
          tf_key: "state-file-name"                ## (Required) AZ backend - Specifies name that will be given to terraform state file and plan artifact
          tf_vars_file: "tfvars-file-name"         ## (Required) Specifies Terraform TFVARS file name inside module path
          enable_TFSEC: true                       ## (Optional)  Enable TFSEC IaC scans (Private repo requires GitHub enterprise)
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID

  Apply_Dev:
    needs: Plan_Dev
    runs-on: ubuntu-latest
    environment: Development #(Optional) If using GitHub Environments      
    steps:
      - name: Dev TF Deploy
        uses: Pwd9000-ML/terraform-azurerm-apply@v1.0.0
        with:
          az_resource_group: "resource-group-name" ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
          az_storage_acc: "storage-account-name"   ## (Required) AZ backend - AZURE terraform backend storage acc 
          az_container_name: "container-name"      ## (Required) AZ backend - AZURE storage container hosting state files 
          tf_key: "state-file-name"                ## (Required) Specifies name of the terraform state file and plan artifact to download
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
```

The terraform plan will be created and is compressed and published to the workflow as an artifact using the same name of the input `tf_key`:  

![image.png](https://raw.githubusercontent.com/Pwd9000-ML/terraform-azurerm-plan/master/assets/artifact.png)  

The terraform apply action will download and apply the artifact created by the plan action using the same `tf_key`.  

**NOTE:** If `enable_TFSEC` is set to `true` on plan stage, Terraform IaC will be scanned using TFSEC and results are published to the GitHub Project `Security` tab:  

![image.png](https://raw.githubusercontent.com/Pwd9000-ML/terraform-azurerm-plan/master/assets/tfsec.png)  

If using a private repository, GitHub enterprise is needed when enabling TFSEC. However if a public repository is used, code analysis is included and TFSEC can be enabled on public repositories without the need for a GitHub enterprise account.  

## Inputs

| Input | Required |Description |Default |
| ----- | -------- | ---------- | ------ |
| `path` | FALSE | Specify path to Terraform module relevant to repo root. | "." |
| `tf_version` | FALSE | Specifies the Terraform version to use. | "latest" |
| `az_resource_group` | TRUE | AZ backend - AZURE Resource Group name hosting terraform backend storage account | N/A |
| `az_storage_acc` | TRUE | AZ backend - AZURE terraform backend storage account name | N/A |
| `az_container_name` | TRUE | AZ backend - AZURE storage container hosting state files  | N/A |
| `tf_key` | TRUE | AZ backend - Specifies name that will be given to terraform state file and plan artifact| N/A |
| `tf_vars_file` | TRUE | Specifies Terraform TFVARS file name inside module path | N/A |
| `enable_TFSEC` | FALSE | Enable IaC TFSEC scan, results are posted to GitHub Project Security Tab. (Private repos require GitHub enterprise). | FALSE |
| `arm_client_id` | TRUE | The Azure Service Principal Client ID | N/A |
| `arm_client_secret` | TRUE | The Azure Service Principal Secret | N/A |
| `arm_subscription_id` | TRUE | The Azure Subscription ID | N/A |
| `arm_tenant_id` | TRUE | The Azure Service Principal Tenant ID | N/A |

## Outputs

None.  

* Plan is uploaded to workflow as an artifact. (Can be deployed using `Pwd9000-ML/terraform-azurerm-apply` Action.)

## Versions of runner that can be used

At the moment only linux runners are supported. Support for all runner types will be released soon.

| Environment | YAML Label |
| --------------------|---------------------|
| Ubuntu 20.04 | `ubuntu-latest` or `ubuntu-20-04` |
| Ubuntu 18.04 | `ubuntu-18.04` |

## License

The associated scripts and documentation in this project are released under the [MIT License](LICENSE).
