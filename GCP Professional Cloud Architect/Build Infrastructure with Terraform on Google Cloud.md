# Build Infrastructure with Terraform on Google Cloud

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


`terraform init`

`terraform plan`
`terraform plan -out plan.txt`

`terraform apply`
