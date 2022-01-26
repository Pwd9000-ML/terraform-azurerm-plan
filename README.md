# Terraform Plan - GitHub Action (terraform-plan-azurerm)

GitHub Action - Creates a Terraform plan with AzureRM backend and uploads plan as workspace artifact.  
Connects to a remote Terraform backend in Azure, creates a terraform plan and uploads plan as a workspace artifact. (Additionally TFSEC IaC scanning can be enabled).  

See my [detailed tutorial]() for more usage details.  

**NOTE:** This plan is a **Child**-Action of **Parent**-Action: **[Pwd9000-ML/terraform-azurerm]()**. Can be used independently as well with **[Pwd9000-ML/terraform-azurerm-apply]()** **Child**-Action.  

## Usage

```yaml
name: "TF-Plan"
on:
  workflow_dispatch:
  pull_request:
    branches:
      - master
jobs:
  Plan_Dev:
    runs-on: ubuntu-latest
    environment: Development #(Optional) If using GitHub Environments      
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Dev TF Plan
        uses: Pwd9000-ML/terraform-azurerm-plan@v1.0.0
        with:
          path: 01_Foundation                ## (Optional) Specify path TF module relevant to repo root. Default="."
          tf_version: "1.1.3"                ## (Optional) Specifies the Terraform version to use. Default="latest"
          az_resource_group: TF-Core-Rg      ## (Required) AZ backend - AZURE Resource Group hosting terraform backend storage acc 
          az_storage_acc: tfcorebackendsa    ## (Required) AZ backend - AZURE terraform backend storage acc 
          az_container_name: ghdeploytfstate ## (Required) AZ backend - AZURE storage container hosting state files 
          tf_key: foundation-dev             ## (Required) AZ backend - Specifies name that will be given to terraform state file 
          tf_vars_file: config-dev.tfvars    ## (Required) Specifies Terraform TFVARS file name inside module path
          enable_TFSEC: true                 ## (Optional)  Enable TFSEC IaC scans
          arm_client_id: ${{ secrets.ARM_CLIENT_ID }}             ## (Required) ARM Client ID 
          arm_client_secret: ${{ secrets.ARM_CLIENT_SECRET }}     ## (Required)ARM Client Secret
          arm_subscription_id: ${{ secrets.ARM_SUBSCRIPTION_ID }} ## (Required) ARM Subscription ID
          arm_tenant_id: ${{ secrets.ARM_TENANT_ID }}             ## (Required) ARM Tenant ID
```

## Inputs

| Input | Required |Description |Default |
| ----- | -------- | ---------- | ------ |
| `path` | FALSE | Specify path TF module relevant to repo root. | "." |
| `tf_version` | FALSE | Specifies the Terraform version to use. | "latest" |
| `az_resource_group` | TRUE | AZ backend - AZURE Resource Group name hosting terraform backend storage account | N/A |
| `az_storage_acc` | TRUE | AZ backend - AZURE terraform backend storage account name | N/A |
| `az_container_name` | TRUE | AZ backend - AZURE storage container hosting state files  | N/A |
| `tf_key` | TRUE | AZ backend - Specifies name that will be given to terraform state file | N/A |
| `tf_vars_file` | TRUE | Specifies Terraform TFVARS file name inside module path | N/A |
| `enable_TFSEC` | FALSE | Enable IaC TFSEC scan, results are posted to GitHub Project Security Tab. | FALSE |
| `arm_client_id` | TRUE | The Azure Service Pricipal Client ID | N/A |
| `arm_client_secret` | TRUE | The Azure Service Pricipal Secret | N/A |
| `arm_subscription_id` | TRUE | The Azure Service Pricipal Subscription ID | N/A |
| `arm_tenant_id` | TRUE | The Azure Service Pricipal Tenant ID | N/A |

## Outputs

None.  

* Plan is uploaded to workspace as an artifact. (Can be deployed using `Pwd9000-ML/terraform-azurerm-apply` Action.)

## Versions of runner that can be used

At the moment only linux runners are supported. Support for all runner types will be released soon.

| Environment | YAML Label |
| --------------------|---------------------|
| Ubuntu 20.04 | `ubuntu-latest` or `ubuntu-20-04` |
| Ubuntu 18.04 | `ubuntu-18.04` |

## License

The associated scripts and documentation in this project are released under the [MIT License](LICENSE).
