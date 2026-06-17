Nightly GCP Terraform Provider

================================================================================

This document describes how to use these custom builds of the Terraform Google
Provider.

## Documentation

A copy of the Terraform Google provider website markdown files are included with
these binaries. The private features available in the binary are documented in
the markdown files as well.

## Adding the provider to the Terraform path

Terraform providers are normally downloaded from releases.hashicorp.com during
Terraform init, so custom providers must be installed manually. The provider
binary in this archive should be moved to the plugin directory in the home of
_the user that will be running the Terraform commands_. The plugin directory is
`~/.terraform.d/plugins` for most systems. If Terraform is being run on a CI
server this will need to be distributed to the instances running the commands.

See the official documentation on [in-house providers](https://developer.hashicorp.com/terraform/language/providers/requirements#in-house-providers)
for more information.

### Sample setup

For a given version x.x.xxxx, and a ${platform} (eg. `linux_amd64`):

* Binary path:
  `~/.terraform.d/plugins/google.com/providers/google-nightly/x.x.xxxx/${platform}`
* `required_providers` block:
  ```
  terraform {
    required_providers {
      google-nightly = {
        source = "google.com/providers/google-nightly"
        version = "x.x.xxxx"
      }
    }
  }
  ```

Note that the binary path is platform-specific. For a full list of paths used by
Terraform, see the
[Implied Local Mirror Directories documentation](https://developer.hashicorp.com/terraform/cli/config/config-file#implied-local-mirror-directories).

## Referencing custom providers in your config

Terraform resources determine the provider to use based on the name of the
resource. For instance the `google_compute_disk` will look for a provider named
`google` first. In order to point a resource to the `google-nightly` provider it
must be explicitly referenced in the resource's config in addition to adding a
provider block in your Terraform config.

ex:
```
provider "google-nightly" {
  credentials = "${file("account.json")}"
  project     = "my-project-id"
  region      = "us-central1"
}

resource "google_compute_instance" "alpha-instance" {
  provider = "google-nightly"

  # ...
}
```

See the official documentation on [alternate provider configurations](https://developer.hashicorp.com/terraform/language/providers/configuration#selecting-alternate-provider-configurations)
and the [`provider` meta-argument](https://developer.hashicorp.com/terraform/language/meta-arguments/resource-provider)
for more information.
