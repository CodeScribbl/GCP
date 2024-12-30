# Build Infrastructure with Terraform on Google Cloud

## Terraform Fundamentals

1) In Cloud Shell, create an empty configuration file named instance.tf with the following command:

`touch instance.tf`

2) Click the instance.tf file and add the following content in it:

```
resource "google_compute_instance" "terraform" {
  project      = "qwiklabs-gcp-03-a4804d0e04b9"
  name         = "terraform"
  machine_type = "e2-medium"
  zone         = "us-east1-c"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }

  network_interface {
    network = "default"
    access_config {
    }
  }
}
```

`terraform init`

`terraform plan`
`terraform plan -out plan.txt`

`terraform apply`


## Infrastructure as Code with Terraform

`touch main.tf`

```
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "3.5.0"
    }
  }
}

provider "google" {

  project = "qwiklabs-gcp-00-5701cb80e0a9"
  region  = "us-west1"
  zone    = "us-west1-c"
}

resource "google_compute_network" "vpc_network" {
  name = "terraform-network"
}
```

`terraform init`
`terraform apply`
`terraform show`

#### Adding resources 

```
resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance"
  machine_type = "e2-micro"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
    }
  }

  network_interface {
    network = google_compute_network.vpc_network.name
    access_config {
    }
  }
}
```

#### Changing resources 

```
resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance"
  machine_type = "e2-micro"
  tags         = ["web", "dev"]
  # ...
}
```

```
student_02_28cb1e98cacf@cloudshell:~ (qwiklabs-gcp-00-5701cb80e0a9)$ terraform plan
google_compute_network.vpc_network: Refreshing state... [id=projects/qwiklabs-gcp-00-5701cb80e0a9/global/networks/terraform-network]
google_compute_instance.vm_instance: Refreshing state... [id=projects/qwiklabs-gcp-00-5701cb80e0a9/zones/us-west1-c/instances/terraform-instance]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  ~ update in-place

Terraform will perform the following actions:

  # google_compute_instance.vm_instance will be updated in-place
  ~ resource "google_compute_instance" "vm_instance" {
        id                   = "projects/qwiklabs-gcp-00-5701cb80e0a9/zones/us-west1-c/instances/terraform-instance"
        name                 = "terraform-instance"
      ~ tags                 = [
          + "dev",
          + "web",
        ]
        # (15 unchanged attributes hidden)

        # (4 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform
apply" now.
```

#### Destructive changes

```
  boot_disk {
    initialize_params {
      image = "cos-cloud/cos-stable"
    }
  }

```

#### Destroy infrastructure

`terraform destroy`

#### Assigning a static IP address

```
resource "google_compute_address" "vm_static_ip" {
  name = "terraform-static-ip"
}
```

```
  network_interface {
    network = google_compute_network.vpc_network.self_link
    access_config {
      nat_ip = google_compute_address.vm_static_ip.address
    }
  }
```

`terraform plan -out static_ip`
Run terraform apply "static_ip" to see how Terraform plans to apply this change:


##### Implicit and explicit dependencies

By studying the resource attributes used in interpolation expressions, Terraform can automatically infer when one resource depends on another. In the example above, the reference to google_compute_address.vm_static_ip.address creates an implicit dependency on the google_compute_address named vm_static_ip.

Terraform uses this dependency information to determine the correct order in which to create and update different resources. In the example above, Terraform knows that the vm_static_ip must be created before the vm_instance is updated to use it.

Implicit dependencies via interpolation expressions are the primary way to inform Terraform about these relationships, and should be used whenever possible.

Sometimes there are dependencies between resources that are not visible to Terraform. The depends_on argument can be added to any resource and accepts a list of resources to create explicit dependencies for.

For example, perhaps an application you will run on your instance expects to use a specific Cloud Storage bucket, but that dependency is configured inside the application code and thus not visible to Terraform. In that case, you can use depends_on to explicitly declare the dependency.

1) Add a Cloud Storage bucket and an instance with an explicit dependency on the bucket by adding the following to main.tf:

```
# New resource for the storage bucket our application will use.
resource "google_storage_bucket" "example_bucket" {
  name     = "<UNIQUE-BUCKET-NAME>"
  location = "US"

  website {
    main_page_suffix = "index.html"
    not_found_page   = "404.html"
  }
}

# Create a new instance that uses the bucket
resource "google_compute_instance" "another_instance" {
  # Tells Terraform that this VM instance must be created only after the
  # storage bucket has been created.
  depends_on = [google_storage_bucket.example_bucket]

  name         = "terraform-instance-2"
  machine_type = "e2-micro"

  boot_disk {
    initialize_params {
      image = "cos-cloud/cos-stable"
    }
  }

  network_interface {
    network = google_compute_network.vpc_network.self_link
    access_config {
    }
  }
}
```

You may wonder where in your configuration these resources should go. The order that resources are defined in a terraform configuration file has no effect on how Terraform applies your changes. Organize your configuration files in a way that makes the most sense for you and your team.


####  Provision infrastructure

The compute instance you launched at this point is based on the Google image given, but has no additional software installed or configuration applied.

Google Cloud allows customers to manage their own custom operating system images. This can be a great way to ensure the instances you provision with Terraform are pre-configured based on your needs. Packer is the perfect tool for this and includes a builder for Google Cloud.

Terraform uses provisioners to upload files, run shell scripts, or install and trigger other software like configuration management tools.

#### Defining a provisioner

1) To define a provisioner, modify the resource block defining the first vm_instance in your configuration to look like the following:

```
resource "google_compute_instance" "vm_instance" {
  name         = "terraform-instance"
  machine_type = "e2-micro"
  tags         = ["web", "dev"]

  provisioner "local-exec" {
    command = "echo ${google_compute_instance.vm_instance.name}:  ${google_compute_instance.vm_instance.network_interface[0].access_config[0].nat_ip} >> ip_address.txt"
  }

  # ...
}
```

This adds a provisioner block within the resource block. Multiple provisioner blocks can be added to define multiple provisioning steps. Terraform supports many provisioners, but for this example you are using the local-exec provisioner.

The local-exec provisioner executes a command locally on the machine running Terraform, not the VM instance itself. You're using this provisioner versus the others so we don't have to worry about specifying any connection info right now.

This also shows a more complex example of string interpolation than you've seen before. Each VM instance can have multiple network interfaces, so refer to the first one with network_interface[0], count starting from 0, as most programming languages do. Each network interface can have multiple access_config blocks as well, so once again you specify the first one.

2) Run terraform apply:

`terraform apply`

At this point, the output may be confusing at first.

Terraform found nothing to do - and if you check, you'll find that there's no ip_address.txt file on your local machine.

Terraform treats provisioners differently from other arguments. Provisioners only run when a resource is created, but adding a provisioner does not force that resource to be destroyed and recreated.

3) Use terraform taint to tell Terraform to recreate the instance:

`terraform taint google_compute_instance.vm_instance`
A tainted resource will be destroyed and recreated during the next apply.

4) Run terraform apply now:

5) Verify everything worked by looking at the contents of the ip_address.txt file.
It contains the IP address, just as you asked.

### Failed provisioners and tainted resources
If a resource is successfully created but fails a provisioning step, Terraform will error and mark the resource as tainted. A resource that is tainted still exists, but shouldn't be considered safe to use, since provisioning failed.

When you generate your next execution plan, Terraform will remove any tainted resources and create new resources, attempting to provision them again after creation.

### Destroy provisioners

Provisioners can also be defined that run only during a destroy operation. These are useful for performing system cleanup, extracting data, etc.

For many resources, using built-in cleanup mechanisms is recommended if possible (such as init scripts), but provisioners can be used if necessary.

This lab won't show any destroyed provisioner examples. If you need to use destroy provisioners, please see the Provisioners documentation.https://www.terraform.io/docs/provisioners/



