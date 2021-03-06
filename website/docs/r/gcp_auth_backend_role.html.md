---
layout: "vault"
page_title: "Vault: vault_auth_backend resource"
sidebar_current: "docs-vault-resource-gcp-auth-backend-role"
description: |-
  Managing roles in an GCP auth backend in Vault
---

# vault\_gcp\_auth\_backend\_role

Provides a resource to create a role in an [GCP auth backend within Vault](https://www.vaultproject.io/docs/auth/gcp.html).

## Example Usage

```hcl
resource "vault_auth_backend" "gcp" {
    path = "gcp"
    type = "gcp"
}

resource "vault_gcp_auth_backend_role" "gcp" {
    backend                = "${vault_auth_backend.cert.path}"
    project_id             = "foo-bar-baz"
    bound_service_accounts = ["database-server@foo-bar-baz.iam.gserviceaccount.com"]
    policies               = ["database-server"]

}
```

## Argument Reference

The following arguments are supported:

* `role` - (Required) Name of the GCP role

* `type` - (Required) Type of GCP authentication role (either `gce` or `iam`)

* `project_id` - (Optional; Deprecated, use `bound_projects` instead) GCP Project that the role exists within

* `bound_projects` - (Optional) An array of GCP project IDs. Only entities belonging to this project can authenticate under the role.

* `ttl` - (Optional) Default TTL of tokens issued by the backend

* `max_ttl` - (Optional) Maximum TTL of tokens issued by the backend

* `period` - (Optional) Duration in seconds for token.  If set, the issued token is a periodic token.

* `policies` - (Optional) Policies to grant on the issued token

* `backend` - (Optional) Path to the mounted GCP auth backend

* `bound_service_accounts` - (Optional) GCP Service Accounts allowed to issue tokens under this role. (Note: **Required** if role is `iam`)

### `iam`-only Parameters

* `max_jwt_exp` - (Optional) The number of seconds past the time of authentication that the login param JWT must expire within. For example, if a user attempts to login with a token that expires within an hour and this is set to 15 minutes, Vault will return an error prompting the user to create a new signed JWT with a shorter `exp`. The GCE metadata tokens currently do not allow the `exp` claim to be customized.

* `allow_gce_inference` - (Optional) A flag to determine if this role should allow GCE instances to authenticate by inferring service accounts from the GCE identity metadata token.

### `gce`-only Parameters

The following parameters are only valid when the role is of type `"gce"`:

* `bound_zones` - (Optional)  The list of zones that a GCE instance must belong to in order to be authenticated. If bound_instance_groups is provided, it is assumed to be a zonal group and the group must belong to this zone.

* `bound_regions` - (Optional) The list of regions that a GCE instance must belong to in order to be authenticated. If bound_instance_groups is provided, it is assumed to be a regional group and the group must belong to this region. If bound_zones are provided, this attribute is ignored.

* `bound_instance_groups` - (Optional) The instance groups that an authorized instance must belong to in order to be authenticated. If specified, either `bound_zones` or `bound_regions` must be set too.

* `bound_labels` - (Optional) A comma-separated list of GCP labels formatted as `"key:value"` strings that must be set on authorized GCE instances. Because GCP labels are not currently ACL'd, we recommend that this be used in conjunction with other restrictions.

* `bound_projects` - (Optional) GCP Projects that the role exists within

For more details on the usage of each argument consult the [Vault GCP API documentation](https://www.vaultproject.io/api/auth/gcp/index.html).

## Attribute Reference

No additional attributes are exposed by this resource.
