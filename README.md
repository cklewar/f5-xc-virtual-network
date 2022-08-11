# F5-XC-VIRTUAL-NETWORK

This repository consists of Terraform templates to bring up a F5XC Virtual-Network.

## Usage

- Clone this repo with: `git clone --recurse-submodules https://github.com/cklewar/f5-xc-virtual-network`
- Enter repository directory with: `cd f5-xc-aws-virtual-network`
- Obtain F5XC API certificate file from Console and save it to `cert` directory
- Pick and choose from below examples and add mandatory input data and copy data into file `main.tf.example`.
- Rename file __main.tf.example__ to __main.tf__ with: `rename main.tf.example main.tf`
- Initialize with: `terraform init`
- Apply with: `terraform apply -auto-approve` or destroy with: `terraform destroy -auto-approve`

### Example Output

```bash
Terraform will perform the following actions:

  # module.tunnel_virtual_network.volterra_virtual_network.virtual_network will be created
  + resource "volterra_virtual_network" "virtual_network" {
      + global_network            = false
      + id                        = (known after apply)
      + name                      = "f5xc-vn-01"
      + namespace                 = "system"
      + site_local_inside_network = false
      + site_local_network        = true

      + static_routes {
          + attrs       = [
              + "ROUTE_ATTR_INSTALL_FORWARDING",
            ]
          + ip_prefixes = [
              + "192.168.0.0/24",
            ]

          + interface {
              + name      = "f5xc-interface-01"
              + namespace = "system"
              + tenant    = "***"
            }
        }
      + static_routes {
          + attrs       = [
              + "ROUTE_ATTR_INSTALL_FORWARDING",
            ]
          + ip_prefixes = [
              + "192.168.1.0/24",
            ]

          + interface {
              + name      = "f5xc-interface-01"
              + namespace = "system"
              + tenant    = "***"
            }
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

## Virtual Network site local network module usage example

```hcl
variable "project_prefix" {
  type        = string
  description = "prefix string put in front of string"
  default     = "f5xc"
}

variable "project_suffix" {
  type        = string
  description = "prefix string put at the end of string"
  default     = "01"
}

variable "f5xc_api_p12_file" {
  type = string
}

variable "f5xc_api_url" {
  type = string
}

variable "f5xc_tenant" {
  type = string
}

module "tunnel_virtual_network" {
  source                            = "./modules/f5xc/virtual-network"
  f5xc_api_p12_file                 = var.f5xc_api_p12_file
  f5xc_api_url                      = var.f5xc_api_url
  f5xc_namespace                    = "system"
  f5xc_tenant                       = var.f5xc_tenant
  f5xc_vn_name                      = format("%s-vn-%s", var.project_prefix, var.project_suffix)
  f5xc_site_local_network           = true
  f5xc_ip_prefixes                  = ["192.168.0.0/24", "192.168.1.0/24"]
  f5xc_ip_prefix_next_hop_interface = format("%s-interface-%s", var.project_prefix, var.project_suffix)
}
```

## Virtual Network global network module usage example

```hcl
variable "project_prefix" {
  type        = string
  description = "prefix string put in front of string"
  default     = "f5xc"
}

variable "project_suffix" {
  type        = string
  description = "prefix string put at the end of string"
  default     = "01"
}

variable "f5xc_api_p12_file" {
  type = string
}

variable "f5xc_api_url" {
  type = string
}

variable "f5xc_tenant" {
  type = string
}

module "global_virtual_network" {
  source              = "./modules/f5xc/virtual-network"
  f5xc_name           = local.global_vn_name
  f5xc_tenant         = var.f5xc_tenant
  f5xc_namespace      = var.f5xc_namespace
  f5xc_global_network = true
}
```



