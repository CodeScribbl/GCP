# Task 1. Create the configuration files
In Cloud Shell, create your Terraform configuration files and a directory structure that resembles the following:
main.tf
variables.tf
modules/
└── instances
    ├── instances.tf
    ├── outputs.tf
    └── variables.tf
└── storage
    ├── storage.tf
    ├── outputs.tf
    └── variables.tf
Copied!
Fill out the variables.tf files in the root directory and within the modules. Add three variables to each file: region, zone, and project_id. For their default values, use , <filled in at lab start>, and your Google Cloud Project ID.
Note: You should use these variables anywhere applicable in your resource configurations.
Add the Terraform block and the Google Provider to the main.tf file. Verify the zone argument is added along with the project and region arguments in the Google Provider block.

Initialize Terraform.

# Solution 1: 

Sure! Below are step-by-step instructions to create the mentioned directory structure and fill out the files as required using Google Cloud's Cloud Shell. 

### Step 1: Create Directory Structure
First, create the directories and files.

```sh
mkdir -p modules/instances
mkdir -p modules/storage
touch main.tf variables.tf
touch modules/instances/instances.tf modules/instances/outputs.tf modules/instances/variables.tf
touch modules/storage/storage.tf modules/storage/outputs.tf modules/storage/variables.tf
```

### Step 2: Create and Fill variables.tf in root and modules
Create variables for `region`, `zone`, and `project_id` in `variables.tf` files.

**Root variables.tf:**
```sh
cat <<EOF > variables.tf
variable "region" {
  description = "The region where resources will be created"
  default     = "<filled in at lab start>"
}

variable "zone" {
  description = "The zone within the region where resources will be created"
  default     = "<filled in at lab start>"
}

variable "project_id" {
  description = "The Google Cloud project ID"
  default     = "<YOUR_PROJECT_ID>"
}
EOF
```

**modules/instances/variables.tf:**
```sh
cat <<EOF > modules/instances/variables.tf
variable "region" {
  description = "The region where resources will be created"
}

variable "zone" {
  description = "The zone within the region where resources will be created"
}

variable "project_id" {
  description = "The Google Cloud project ID"
}
EOF
```

**modules/storage/variables.tf:**
```sh
cat <<EOF > modules/storage/variables.tf
variable "region" {
  description = "The region where resources will be created"
}

variable "zone" {
  description = "The zone within the region where resources will be created"
}

variable "project_id" {
  description = "The Google Cloud project ID"
}
EOF
```

### Step 3: Fill Terraform block and Google Provider in main.tf
Add the Terraform block and the Google provider configuration in the `main.tf` file.

**main.tf:**
```sh
cat <<EOF > main.tf
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 3.5"
    }
  }
  
  required_version = ">= 0.12"
}

provider "google" {
  project = var.project_id
  region  = var.region
  zone    = var.zone
}
EOF
```

### Step 4: Initialize Terraform
Initialize terraform inside the Cloud Shell.

```sh
terraform init
```

This will initialize the required directories and providers mentioned in your configuration.

By following these steps, you will have set up the required Terraform directory structure and initialized Terraform within Google Cloud Shell successfully.
