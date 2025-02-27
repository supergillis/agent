---
# NOTE(rfratto): the title below has zero-width spaces injected into it to
# prevent it from overflowing the sidebar on the rendered site. Be careful when
# modifying this section to retain the spaces.
#
# Ideally, in the future, we can fix the overflow issue with css rather than
# injecting special characters.

title: prometheus.​integration.​apache
---

# prometheus.integration.apache
The `prometheus.integration.apache` component embeds
[apache_exporter](https://github.com/Lusitaniae/apache_exporter) for collecting mod_status statistics from an apache server.

## Usage

```river
prometheus.integration.apache "LABEL"{
}
```

## Arguments
The following arguments can be used to configure the exporter's behavior.
All arguments are optional. Omitted fields take their default values.

Name | Type | Description | Default | Required
---- | ---- | ----------- | ------- | --------
`scrape_uri`    | `string` | URI to Apache stub status page. | `http://localhost/server-status?auto` | no
`host_override` | `string` | Override for HTTP Host header.  | | no
`insecure`      | `bool`   | Ignore server certificate if using https. | false | no

## Exported fields
The following fields are exported and can be referenced by other components.

Name      | Type                | Description
--------- | ------------------- | -----------
`targets` | `list(map(string))` | The targets that can be used to collect `consul` metrics.

For example, the `targets` can either be passed to a `prometheus.relabel`
component to rewrite the metric's label set, or to a `prometheus.scrape`
component that collects the exposed metrics.

## Component health

`prometheus.integration.apache` is only reported as unhealthy if given
an invalid configuration. In those cases, exported fields retain their last
healthy values.

## Debug information

`prometheus.integration.apache` does not expose any component-specific
debug information.

## Debug metrics

`prometheus.integration.apache` does not expose any component-specific
debug metrics.

## Example

This example uses a [`prometheus.scrape` component][scrape] to collect metrics
from `prometheus.integration.apache`:

```river
prometheus.integration.apache "example" {
  scrape_uri = "http://web.example.com/server-status?auto"
}

// Configure a prometheus.scrape component to collect consul metrics.
prometheus.scrape "demo" {
  targets    = prometheus.integration.apache.example.targets
  forward_to = [ /* ... */ ]
}
```

[scrape]: {{< relref "./prometheus.scrape.md" >}}
