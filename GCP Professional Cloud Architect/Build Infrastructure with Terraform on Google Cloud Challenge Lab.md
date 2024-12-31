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


# Task 2. Import infrastructure
In the Google Cloud Console, on the Navigation menu, click Compute Engine > VM Instances. Two instances named tf-instance-1 and tf-instance-2 have already been created for you.
Note: by clicking on one of the instances, you can find its Instance ID, boot disk image, and machine type. These are all necessary for writing the configurations correctly and importing them into Terraform.
Import the existing instances into the instances module. To do this, you will need to follow these steps:
First, add the module reference into the main.tf file then re-initialize Terraform.
Next, write the resource configurations in the instances.tf file to match the pre-existing instances.
Name your instances tf-instance-1 and tf-instance-2.
For the purposes of this lab, the resource configuration should be as minimal as possible. To accomplish this, you will only need to include the following additional arguments in your configuration: machine_type, boot_disk, network_interface, metadata_startup_script, and allow_stopping_for_update. For the last two arguments, use the following configuration as this will ensure you won't need to recreate it:
```
metadata_startup_script = <<-EOT
        #!/bin/bash
    EOT
allow_stopping_for_update = true
```
Once you have written the resource configurations within the module, use the terraform import command to import them into your instances module.
Apply your changes. Note that since you did not fill out all of the arguments in the entire configuration, the apply will update the instances in-place. This is fine for lab purposes, but in a production environment, you should make sure to fill out all of the arguments correctly before importing.
Click Check my progress to verify the objective.

# Solution 2

Sure! Let's follow the steps you mentioned to import the existing VM instances into Terraform.

### Step 1: Add the Module Reference in the main.tf File

First, add the module reference for instances in the `main.tf` file.

```sh
cat <<EOF >> main.tf
module "instances" {
  source    = "./modules/instances"
  region    = var.region
  zone      = var.zone
  project_id  = var.project_id
}
EOF
```

### Step 2: Reinitialize Terraform

Run `terraform init` again to reinitialize terraform with the new module.

```sh
terraform init
```

### Step 3: Write Resource Configurations in instances.tf

Write the resource configurations to match the pre-existing instances in `modules/instances/instances.tf`.

```sh
cat <<EOF > modules/instances/instances.tf
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "n1-standard-1"
  zone         = var.zone

  boot_disk {
    initialize_params {
      image = "projects/debian-cloud/global/images/family/debian-10"
    }
  }

  network_interface {
    network = "default"
  }

  metadata_startup_script = <<-EOT
        #!/bin/bash
  EOT

  allow_stopping_for_update = true
}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "n1-standard-1"
  zone         = var.zone

  boot_disk {
    initialize_params {
      image = "projects/debian-cloud/global/images/family/debian-10"
    }
  }

  network_interface {
    network = "default"
  }

  metadata_startup_script = <<-EOT
        #!/bin/bash
  EOT

  allow_stopping_for_update = true
}
EOF
```

### Step 4: Import the Existing Instances

Use the `terraform import` command to import the existing instances. Make sure to replace `<YOUR_PROJECT_ID>`, `<YOUR_ZONE>`, and `<INSTANCE_ID>` with the actual values from your Google Cloud Console.

```sh
terraform import module.instances.google_compute_instance.tf-instance-1 projects/<YOUR_PROJECT_ID>/zones/<YOUR_ZONE>/instances/tf-instance-1
terraform import module.instances.google_compute_instance.tf-instance-2 projects/<YOUR_PROJECT_ID>/zones/<YOUR_ZONE>/instances/tf-instance-2
```

### Step 5: Apply Your Changes

Finally, apply your changes using `terraform apply`.

```sh
terraform apply
```

Follow the prompt to confirm the apply operation.

### Note:
- Ensure you gather the actual `machine_type`, `boot_disk` image, `network_interface`, `metadata_startup_script`, and `allow_stopping_for_update` from the actual instance properties visualized on the Console before running `terraform apply`.
- This example provided some values (e.g., machine type, boot disk image) for demonstration purposes. Make sure you replace them with the values that match your actual instances on the Google Cloud.

If completed successfully, the existing Google Cloud instances will now be managed by Terraform, matching the provided configurations in your module.

# Task 3. Configure a remote backend
Create a Cloud Storage bucket resource inside the storage module. For the bucket name, use Bucket Name. For the rest of the arguments, you can simply use:

location = "US"
force_destroy = true
uniform_bucket_level_access = true
Note: You can optionally add output values inside of the outputs.tf file.
Add the module reference to the main.tf file. Initialize the module and apply the changes to create the bucket using Terraform.

Configure this storage bucket as the remote backend inside the main.tf file. Be sure to use the prefix terraform/state so it can be graded successfully.

If you've written the configuration correctly, upon init, Terraform will ask whether you want to copy the existing state data to the new backend. Type yes at the prompt.

Click Check my progress to verify the objective.

# Solution 3 :

Sure, let's go through the steps to accomplish this task.

### Step 1: Write Configuration for Cloud Storage Bucket

Inside the `modules/storage/storage.tf` file, define the Cloud Storage Bucket resource.

**storage.tf:**
```sh
cat <<EOF > modules/storage/storage.tf
resource "google_storage_bucket" "bucket" {
  name = "Bucket Name"
  location = "US"
  force_destroy = true
  uniform_bucket_level_access = true
}
EOF
```

### Step 2: Optionally Add Output Values

You can add output values inside the `modules/storage/outputs.tf` file.

**outputs.tf:**
```sh
cat <<EOF > modules/storage/outputs.tf
output "bucket_name" {
  value = google_storage_bucket.bucket.name
}
EOF
```

### Step 3: Add Module Reference to main.tf

Add the module reference for storage in the `main.tf` file.

**main.tf:**
```sh
cat <<EOF >> main.tf
module "storage" {
  source     = "./modules/storage"
  region     = var.region
  project_id = var.project_id
}
EOF
```

### Step 4: Initialize the Module and Apply Changes

Reinitialize Terraform and apply changes to create the bucket using Terraform.

```sh
terraform init
terraform apply -auto-approve
```

### Step 5: Configure Remote Backend

Configure the storage bucket as the remote backend in the `main.tf` file. Add the backend configuration block.

**main.tf:**
```sh
cat <<EOF >> main.tf
terraform {
  backend "gcs" {
    bucket = "Bucket Name"
    prefix = "terraform/state"
  }
}
EOF
```

### Step 6: Reinitialize the Backend

Reinitialize terraform to use the new remote backend.

```sh
terraform init
```

When prompted to copy the existing state data to the new backend, type `yes`.

```sh
Initializing the backend...
Do you want to copy existing state to the new backend? (yes/no)
> yes
```

### Step 7: Verify

The state should now be stored in the specified remote backend bucket. To verify the remote backend configuration, you can check the GCS bucket for the state file and run `terraform plan` to ensure everything is configured correctly.

If you've followed these steps correctly, your Google Cloud Storage bucket will now serve as the remote backend for Terraform state, and you can verify the objective by clicking "Check my progress."

This setup ensures that the state file is stored remotely in a secure and centralized location, enhancing collaboration and state management for Terraform projects.

# Task 4. Modify and update infrastructure
Navigate to the instances module and modify the tf-instance-1 resource to use an e2-standard-2 machine type.

Modify the tf-instance-2 resource to use an e2-standard-2 machine type.

Add a third instance resource and name it Instance Name. For this third resource, use an e2-standard-2 machine type. Make sure to change the machine type to e2-standard-2 to all the three instances.

Initialize Terraform and apply your changes.


# Solution 4

Sure, let's go ahead and make the required changes to the `instances.tf` file within the `modules/instances` directory.

### Step 1: Modify `instances.tf` for Machine Types and Add Third Instance

Edit the `instances.tf` file to update the machine types and add a third instance resource.

**modules/instances/instances.tf:**
```sh
cat <<EOF > modules/instances/instances.tf
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "e2-standard-2"
  zone         = var.zone

  boot_disk {
    initialize_params {
      image = "projects/debian-cloud/global/images/family/debian-10"
    }
  }

  network_interface {
    network = "default"
  }

  metadata_startup_script = <<-EOT
        #!/bin/bash
  EOT

  allow_stopping_for_update = true
}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "e2-standard-2"
  zone         = var.zone

  boot_disk {
    initialize_params {
      image = "projects/debian-cloud/global/images/family/debian-10"
    }
  }

  network_interface {
    network = "default"
  }

  metadata_startup_script = <<-EOT
        #!/bin/bash
  EOT

  allow_stopping_for_update = true
}

resource "google_compute_instance" "instance-3" {
  name         = "instance-3"
  machine_type = "e2-standard-2"
  zone         = var.zone

  boot_disk {
    initialize_params {
      image = "projects/debian-cloud/global/images/family/debian-10"
    }
  }

  network_interface {
    network = "default"
  }

  metadata_startup_script = <<-EOT
        #!/bin/bash
  EOT

  allow_stopping_for_update = true
}
EOF
```

### Step 2: Initialize Terraform

Reinitialize Terraform to make sure everything is up to date.

```sh
terraform init
```

### Step 3: Apply Changes

Apply the changes to update the instances and create the new one.

```sh
terraform apply -auto-approve
```

This will update `tf-instance-1` and `tf-instance-2` to use the `e2-standard-2` machine type and create the third instance with the same machine type.

### Notes:
- It is essential to ensure the correct values for the machine types, boot disks, and network interfaces as seen from the actual configurations in your Google Cloud Console.
- Also, if there are any changes to the metadata_startup_script or other parameters inside your existing infrastructure, they should be added accordingly.

By following these steps, you will have successfully modified the existing instances and added a new instance with the required configurations using Terraform. If everything runs correctly, the configurations will reflect accurately on the Google Cloud Console.

# Task 5 Task 6. Use a module from the Registry
In the Terraform Registry, browse to the Network Module.

Add this module to your main.tf file. Use the following configurations:

Use version 6.0.0 (different versions might cause compatibility errors).
Name the VPC VPC Name, and use a global routing mode.
Specify 2 subnets in the region, and name them subnet-01 and subnet-02. For the subnets arguments, you just need the Name, IP, and Region.
Use the IP 10.10.10.0/24 for subnet-01, and 10.10.20.0/24 for subnet-02.
You do not need any secondary ranges or routes associated with this VPC, so you can omit them from the configuration.
Once you've written the module configuration, initialize Terraform and run an apply to create the networks.

Next, navigate to the instances.tf file and update the configuration resources to connect tf-instance-1 to subnet-01 and tf-instance-2 to subnet-02.

# Solution 5: 

Alright, let's proceed with the task step-by-step. We'll start with adding the network module to the `main.tf` file, initializing Terraform, and then applying the changes.

### Step 1: Add Network Module to `main.tf`

First, browse to the Terraform Registry to locate the Google Cloud Network module: [Terraform Registry Network Module](https://registry.terraform.io/modules/terraform-google-modules/network/google/latest).

Use the provided configurations:

**main.tf:**
```sh
cat <<EOF >> main.tf
module "vpc" {
  source  = "terraform-google-modules/network/google"
  version = "6.0.0"

  project_id   = var.project_id
  network_name = "VPC Name"
  routing_mode = "GLOBAL"

  subnets = [
    {
      subnet_name   = "subnet-01"
      subnet_ip     = "10.10.10.0/24"
      subnet_region = var.region
    },
    {
      subnet_name   = "subnet-02"
      subnet_ip     = "10.10.20.0/24"
      subnet_region = var.region
    }
  ]

  secondary_ranges = null
  routes = null
}
EOF
```

### Step 2: Initialize Terraform

Initialize Terraform to install the new module:

```sh
terraform init
```

### Step 3: Apply Changes

Apply the changes to create the networks:

```sh
terraform apply -auto-approve
```

### Step 4: Update instances.tf to Connect Instances to Subnets

Next, update the configuration resources in the `instances.tf` file to connect `tf-instance-1` to `subnet-01` and `tf-instance-2` to `subnet-02`.

**modules/instances/instances.tf:**
```sh
cat <<EOF > modules/instances/instances.tf
resource "google_compute_instance" "tf-instance-1" {
  name         = "tf-instance-1"
  machine_type = "e2-standard-2"
  zone         = var.zone

  boot_disk {
    initialize_params {
      image = "projects/debian-cloud/global/images/family/debian-10"
    }
  }

  network_interface {
    subnetwork = module.vpc.subnets[0]
  }

  metadata_startup_script = <<-EOT
        #!/bin/bash
  EOT

  allow_stopping_for_update = true
}

resource "google_compute_instance" "tf-instance-2" {
  name         = "tf-instance-2"
  machine_type = "e2-standard-2"
  zone         = var.zone

  boot_disk {
    initialize_params {
      image = "projects/debian-cloud/global/images/family/debian-10"
    }
  }

  network_interface {
    subnetwork = module.vpc.subnets[1]
  }

  metadata_startup_script = <<-EOT
        #!/bin/bash
  EOT

  allow_stopping_for_update = true
}

resource "google_compute_instance" "instance-3" {
  name         = "instance-3"
  machine_type = "e2-standard-2"
  zone         = var.zone

  boot_disk {
    initialize_params {
      image = "projects/debian-cloud/global/images/family/debian-10"
    }
  }

  network_interface {
    subnetwork = module.vpc.subnets[1] // Change it as per your need
  }

  metadata_startup_script = <<-EOT
        #!/bin/bash
  EOT

  allow_stopping_for_update = true
}
EOF
```

### Step 5: Apply Changes Again

Apply the changes again to update the instances with the new subnet configurations:

```sh
terraform apply -auto-approve
```

By following these steps, you will have successfully added the network module, assigned instances to specific subnets, and applied the network configurations to your Terraform-managed infrastructure on Google Cloud.

# Task 6  Configure a firewall
Create a firewall rule resource in the main.tf file, and name it tf-firewall.
This firewall rule should permit the VPC Name network to allow ingress connections on all IP ranges (0.0.0.0/0) on TCP port 80.
Make sure you add the source_ranges argument with the correct IP range (0.0.0.0/0).
Initialize Terraform and apply your changes.

# Solution 6 

Sure, let's proceed with adding the firewall rule to the `main.tf` file. This rule will allow ingress connections from all IP ranges to TCP port 80 on your VPC.

### Step 1: Add Firewall Rule to `main.tf`

Add the firewall rule configuration to your `main.tf`:

**main.tf:**
```sh
cat <<EOF >> main.tf
resource "google_compute_firewall" "tf-firewall" {
  name    = "tf-firewall"
  network = module.vpc.network_name

  allow {
    protocol = "tcp"
    ports    = ["80"]
  }

  source_ranges = ["0.0.0.0/0"]
}
EOF
```

### Step 2: Initialize Terraform

Reinitialize Terraform to include the new firewall rule configuration:

```sh
terraform init
```

### Step 3: Apply Changes

Apply the changes to create the firewall rule:

```sh
terraform apply -auto-approve
```

This will create a firewall rule named `tf-firewall`, allowing ingress connections on TCP port 80 from all IP ranges (0.0.0.0/0) to your VPC. 

By following these steps, you will have successfully configured the firewall rule and applied it to your Terraform-managed infrastructure on Google Cloud.
