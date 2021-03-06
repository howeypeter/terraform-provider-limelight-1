---
layout: "limelight"
page_title: "Provider: Limelight"
sidebar_current: "docs-limelight-index"
description: |-
  Terraform Provider for Limelight Networks
---

# Limelight Provider

The Limelight provider is used to interact with Limelight Networks resources including
CDN configurations, EdgeFunctions and Realtime Streaming APIs.

More details can be found on the Control [Documentation page](https://control.llnw.com/acontrol/#/documentation)
as well as on the [Developer Central page](https://limelight.service-now.com/community?id=community_home)

Use the navigation to the left to read about the available resources and data sources.

## Basic Configuration

In order to use the Limelight Networks Provider you first need a Limelight Networks account user
with the appropriate permissions and an API key. An account user can be setup via the
`Limelight CONTROL` dashboard and must have specific permissions depending on the resources
you plan to manage with the provider:

* `Manage` permissions for `Caching & Delivery` as well as `Config API Access` if you plan to use the
  Limelight Delivery resource.
* `Manage` permissions for `Live Streaming` as well as `Config API Access` if you plan to use the
  Realtime Streaming resource.
* `Manage` permissions for `EdgeFunctions` if you plan to use the EdgeFunctions resource.

To obtain an API key, log in to `Limelight CONTROL` and select `My Account` to view your
account page in the dashboard. From there you can use the `Show my Shared Key` or
`Generate a new Shared Key` to obtain the API key necessary for the provider. Keep this key
private.

## Example Usage

```hcl
# Configure the Limelight Provider
provider "limelight" {
  username                   = var.llnw_username
  api_key                    = var.llnw_api_key
}

provider "archive" {}

# The archive file containing your EdgeFunction code
data "archive_file" "function_archive" {
  type        = "zip"
  source_dir  = "function"
  output_path = "function.zip"
}

# An EdgeFunction created from the zip archive above
resource "limelight_edgefunction" "hello_world" {
  shortname        =  var.shortname
  name             = "hello_world_terraform"
  description      = "A simple hello world function, provisioned with Terraform"
  function_archive = data.archive_file.function_archive.output_path
  function_sha256  = filesha256(data.archive_file.function_archive.output_path)
  handler          = "hello_world.handler"
  runtime          = "python3"
  memory           = 256
  timeout          = 4000
  can_debug        = true
  environment_variable {
    name  = "NAME"
    value = "World"
  }
}

```

## Argument Reference

These arguments are supported in the Limelight `provider` block:

* `config_api_base_url` - (Optional) The base URL to the Limelight Configuration API without the trailing slash
  included. This value can also be set via the `LLNW_CONFIG_API_URL` environment variable.

* `edgefunctions_api_base_url` - (Optional) The base URL to the Limelight EdgeFunctions API without the trailing slash
  included. This value can also be set via the `LLNW_EDGEFUNCTIONS_API_URL` environment variable.

* `username` - (Required) Your Limelight Networks username. This value can also be set via the `LLNW_API_USERNAME`
  environment variable.

* `api_key` - (Required) The shared API key for your username. This value can also be set via the `LLNW_API_KEY`
  environment variable.
