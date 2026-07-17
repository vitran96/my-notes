[[CI CD tools]] for [[Infrastructure]], known as [[Infrastructure as Code]].

# Deploy

```
tofu apply --auto-approve -target=proxmox_virtual_environment_vm.jenkins
```

# Standard project structure

```plaintext
# ==============================================================================
# STANDARD TERRAFORM PROJECT STRUCTURE (Multi-Environment with Modules)
# ==============================================================================
# This layout separates environments (Dev, Prod) and keeps code DRY (Don't Repeat
# Yourself) by utilizing local or remote modules.
#
terraform-project/
├── .gitignore               # Ignore local state files, variable files (.tfvars), and .terraform/
├── README.md                # Documentation of the infrastructure architecture
├── modules/                 # Reusable infrastructure components
│   ├── vpc/
│   │   ├── main.tf          # Core resource definitions for VPC
│   │   ├── variables.tf     # Input variables for VPC module
│   │   ├── outputs.tf       # Exported values (e.g., subnet IDs)
│   │   └── README.md
│   └── ec2/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
└── environments/            # Environment-specific configurations
    ├── dev/
    │   ├── main.tf          # Instantiates modules using dev-specific inputs
    │   ├── variables.tf     # Dev-specific variable declarations
    │   ├── outputs.tf       # Dev outputs
    │   ├── backend.tf       # Dev state storage configuration (e.g., S3, Consul)
    │   └── terraform.tfvars # Actual values for dev variables (e.g., instance_type = "t3.micro")
    └── prod/
        ├── main.tf          # Instantiates modules using prod-specific inputs
        ├── variables.tf
        ├── outputs.tf
        ├── backend.tf
        └── terraform.tfvars # Actual values for prod variables (e.g., instance_type = "m5.large")
```