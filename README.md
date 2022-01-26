# terraform-plan-azurerm (Terraform Plan - GitHub Action)
GitHub Action - Creates a Terraform plan with AzureRM backend and uploads plan as workspace artifact.  
Connects to a remote Terraform backend in Azure, creates a terraform plan and uploads plan as a workspace artifact. (Additionally TFSEC IaC scanning can be enabled).  

**NOTE:** This plan is a **Child**-Action of **Parent**-Action: **[Pwd9000-ML/terraform-azurerm]()**. (Can be used independently as well).  

See my [detailed tutorial]() for more usage details.  
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

| Input | Description |
| ----- | ----------- |
| `path` | **Optional** Specify path TF module relevant to repo root. Default="." |
| `tf_version` | **Optional** Specifies the Terraform version to use. Default="latest" |
| `az_resource_group` | **Required** AZ backend - AZURE Resource Group name hosting terraform backend storage account |
| `az_storage_acc` | **Required** AZ backend - AZURE terraform backend storage account name |
| `az_container_name` | **Required** AZ backend - AZURE storage container hosting state files  |
| `tf_key` | **Required** AZ backend - Specifies name that will be given to terraform state file |
| `tf_vars_file` | **Required** Specifies Terraform TFVARS file name inside module path |
| `enable_TFSEC` | **Optional** Enable IaC TFSEC scan, results are posted to GitHub Project Security Tab. Default=false |
| `arm_client_id` | **Required** The Azure Service Pricipal Client ID |
| `arm_client_secret` | **Required** The Azure Service Pricipal Secret |
| `arm_subscription_id` | **Required** The Azure Service Pricipal Subscription ID |
| `arm_tenant_id` | **Required** The Azure Service Pricipal Tenant ID |

## Outputs

None.  

* Plan is uploaded to workspace as an artifact. (Can be deployed using `Pwd9000-ML/terraform-azurerm-apply` Action.)
