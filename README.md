# AMD Property Module

Reusable Terraform module for creating an Akamai Adaptive Media Delivery property.

## Resources

The module creates:

- One CP code.
- Edge hostnames for the selected mode, except secure SBD where Akamai provisions the hostname automatically.
- An AMD rule tree with origin, CP code, media characteristics, segmented media optimization, HTTP/3, optional enhanced debug, and optional CORS.
- Optional additional-origin child rules selected by hostname and/or path.
- A property and optional staging/production activations.

## Hostname and certificate modes

| `edge_hostname_type` | Edge hostname | Certificate input | EHN resource |
| --- | --- | --- | --- |
| `SBD`, `etls = true` | `edgekey.net` | Automatic SBD | No |
| `SBD`, `etls = false` | `edgesuite.net` | Automatic Standard TLS | Yes |
| `EDGESUITE` | `edgesuite.net` | Automatic Standard TLS | Yes |
| `EDGEKEY` | `edgekey.net` | `certificate_id` required | Yes |
| `AKAMAIZED_HOSTNAME` | `akamaized.net` | Shared Cert path | Yes |

For `AKAMAIZED_HOSTNAME`, the input is a 4-63 character label. Terraform appends `.akamaized.net` for both property and edge-hostname values.

## Activation

Production activation requires exactly one `noncompliance_reason`. The module renders the corresponding compliance-record block and always acknowledges Property Manager rule warnings. Existing activation resources can be preserved with `activation_to_staging_exists` and `activation_to_production_exists`.

## Additional origins

`additional_origins` is a map of named origin rules. Each object contains `origin_name`, `forward_host_header`, `hostname_match`, and `path_match`. The map defaults to empty, preserving the default origin-only behavior. This follows the delivery module's additional-origin contract.

## Rule format

The AMD rule tree is pinned to the provider-supported `v2026-02-16` builder schema. AMD-specific behavior is kept in the rules submodule; the root module owns Akamai resources and lifecycle.
