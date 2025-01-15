# Lab Notes

`terraform -version`
`mkdir tfinfra && cd $_`
`touch provider.tf`

```

  provider "google" {
  project = "qwiklabs-gcp-04-a97417cd48af"
  region  = "us-east1"
  zone    = "us-east1-b"
}

```

`terraform init`

`touch instance.tf`
`vi instance.tf`

```resource google_compute_instance "vm_instance" {
name         = "${var.instance_name}"
zone         = "${var.instance_zone}"
machine_type = "${var.instance_type}"
boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
      }
  }
 network_interface {
    network = "default"
    access_config {
      # Allocate a one-to-one NAT IP to the instance
    }
  }
}
```


`touch variables.tf`
`vi variables.tf`

```
variable "instance_name" {
  type        = string
  description = "Name for the Google Compute instance"
}
variable "instance_zone" {
  type        = string
  description = "Zone for the Google Compute instance"
}
variable "instance_type" {
  type        = string
  description = "Disk type of the Google Compute instance"
  default     = "e2-medium"
  }
```

`touch outputs.tf`
`vi outputs.tf`

```
output "network_IP" {
  value = google_compute_instance.vm_instance.instance_id
  description = "The internal ip address of the instance"
}
output "instance_link" {
  value = google_compute_instance.vm_instance.self_link
  description = "The URI of the created resource."
}
```


```
 resource "google_compute_address" "vm_static_ip" {
  name = "terraform-static-ip"
}
 resource google_compute_instance "vm_instance" {
name         = "${var.instance_name}"
zone         = "${var.instance_zone}"
machine_type = "${var.instance_type}"
boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
      }
  }
 network_interface {
    network = "default"
    access_config {
      # Allocate a one-to-one NAT IP to the instance
      nat_ip = google_compute_address.vm_static_ip.address
    }
  }
}
```

`terraform init`
`terraform apply`

- var.instance_name: `myinstance`
- var.instance_zone: `us-east1-b`

`touch exp.tf`

```
# Create a new instance that uses the bucket
resource "google_compute_instance" "another_instance" {

  name         = "terraform-instance-2"
  machine_type = "e2-micro"
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
  # Tells Terraform that this VM instance must be created only after the
  # storage bucket has been created.
  depends_on = [google_storage_bucket.example_bucket]
}
```

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
```

`terraform graph | dot -Tpng > graph.png`

--------------
### Lab3 -  Automating the Deployment of Infrastructure Using Terraform

--------------


`terraform --version`
`mkdir tfinfra`
`provider "google" {}`

`cd tfinfra`
`terraform init`

`touch mynetwork.tf`
`vi mynetwork.tf`

```
# Create the mynetwork network
resource [RESOURCE_TYPE] "mynetwork" {
name = [RESOURCE_NAME]
# RESOURCE properties go here
}
```


```
# Create the mynetwork network
resource "google_compute_network" "mynetwork" {
name = "mynetwork"
# RESOURCE properties go here
auto_create_subnetworks = "true"
}
```


```
# Add a firewall rule to allow HTTP, SSH, RDP and ICMP traffic on mynetwork
resource [RESOURCE_TYPE] "mynetwork-allow-http-ssh-rdp-icmp" {
name = [RESOURCE_NAME]
# RESOURCE properties go here
}
```

```
# Add a firewall rule to allow HTTP, SSH, RDP and ICMP traffic on mynetwork
resource "google_compute_firewall" "mynetwork-allow-http-ssh-rdp-icmp" {
name = "mynetwork-allow-http-ssh-rdp-icmp"
# RESOURCE properties go here
network = google_compute_network.mynetwork.self_link
allow {
    protocol = "tcp"
    ports    = ["22", "80", "3389"]
    }
allow {
    protocol = "icmp"
    }
source_ranges = ["0.0.0.0/0"]
}
```

`mkdir instance`
`touch main.tf`

```
resource [RESOURCE_TYPE] "vm_instance" {
  name = [RESOURCE_NAME]
  # RESOURCE properties go here
}
```

```
resource "google_compute_instance" "vm_instance" {
  name         = "${var.instance_name}"
  zone         = "${var.instance_zone}"
  machine_type = "${var.instance_type}"
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
      }
  }
  network_interface {
    network = "${var.instance_network}"
    access_config {
      # Allocate a one-to-one NAT IP to the instance
    }
  }
}
```

`touch variables.tf`
`vi variables.tf`

```
variable "instance_name" {}
variable "instance_zone" {}
variable "instance_type" {
  default = "e2-micro"
  }
variable "instance_network" {}
```

```

# Create the mynet-vm-1 instance
module "mynet-vm-1" {
  source           = "./instance"
  instance_name    = "mynet-vm-1"
  instance_zone    = "Zone"
  instance_network = google_compute_network.mynetwork.self_link
}

# Create the mynet-vm-2" instance
module "mynet-vm-2" {
  source           = "./instance"
  instance_name    = "mynet-vm-2"
  instance_zone    = "Zone 2"
  instance_network = google_compute_network.mynetwork.self_link
}
```

---------
 ### Lab 4 -  Creating a Remote Backend
---------

`terraform --version`

`touch main.tf`

`gcloud config list --format 'value(core.project)'`

`vi main.tf`

```
provider "google" {
  project     = "qwiklabs-gcp-02-87b467233315"
  region      = "us-west1"
}
resource "google_storage_bucket" "test-bucket-for-state" {
  name        = "qwiklabs-gcp-02-87b467233315"
  location    = "US" # Replace with EU for Europe region
  uniform_bucket_level_access = true
}
terraform {
  backend "local" {
    path = "terraform/state/terraform.tfstate"
  }
}
```

```
terraform init
terraform apply
terraform show
```

```
terraform {
  backend "gcs" {
    bucket  = "qwiklabs-gcp-02-87b467233315"
    prefix  = "terraform/state"
  }
}
```

`terraform init -migrate-state`

### References 

- [https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli "https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli")
- [https://developer.hashicorp.com/terraform/language/style](https://developer.hashicorp.com/terraform/language/style "https://developer.hashicorp.com/terraform/language/style")
- [https://registry.terraform.io/browse/modules?provider=google](https://registry.terraform.io/browse/modules?provider=google "https://registry.terraform.io/browse/modules?provider=google")
- [https://github.com/roitraining/hands-on-terraform](https://github.com/roitraining/hands-on-terraform "https://github.com/roitraining/hands-on-terraform")
- [mike.sutton@roitraining.com](mailto:mike.sutton@roitraining.com "mailto:mike.sutton@roitraining.com")
- [https://spacelift.io/blog/infrastructure-as-code-tools](https://spacelift.io/blog/infrastructure-as-code-tools "https://spacelift.io/blog/infrastructure-as-code-tools")

