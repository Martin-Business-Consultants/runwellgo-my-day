# My day for Runwell

The work you lead or are on in one list, searched and filtered, with the piece you pick open beside it, and the work you drag to the top as your priorities, in the order you mean to do it. It keeps the Rails plugin's tables (`my_day_bundles`, `my_day_pins`), so an imported Rails database's priorities are its own.

A [Runwell](https://github.com/Martin-Business-Consultants/runwellgo) plugin: key `my_day`,
version 0.1.0. It's in the official Runwell binary; an owner adds it from
**Settings › Plugins › Add a plugin** and switches it on, which grants what it asks for.

## What it asks for

- `records:read`: reads clients, engagements, work and the other core records, as agents see them
- `records:write`: writes through the core's own rules (notes, work, commitments, events), recorded as whoever made the call
- `tables`: keeps its own SQL tables (its manifest's Schema)

The grants are a contract the host keeps: the plugin reaches Runwell only through the
`*plugin.API` it's handed, holding what an owner granted ([docs/plugins.md](https://github.com/Martin-Business-Consultants/runwellgo/blob/main/docs/plugins.md)).

## Its screens

Its own screens are Svelte, in [`ui/`](ui/): `ui/index.js` exports the plugin's `key`, its `routes` (each shown only while the plugin is on) and a `setup()` that adds its tabs and slots. They're part of the official Runwell app: runwellgo's `make plugin-ui` copies this folder into the app's `ui/src/plugins/my_day/` (from the module `go list -m` finds), and the app is built with it. They import the app's shared pieces (`$lib/…`, shadcn components) and talk only to the plugin's own routes, `/api/x/my_day/…`.

## Building Runwell with it

The official binary (runwellgo's `cmd/runwell`) already has it. A Runwell of your own is a
`main.go` that hands `runwell.Main` its plugins:

```go
package main

import (
	"os"

	myday "github.com/Martin-Business-Consultants/runwellgo-my-day"
	"github.com/Martin-Business-Consultants/runwellgo/v3/runwell"
)

// version is set from the release tag: go build -ldflags "-X main.version=1.2.3".
var version = "dev"

func main() {
	os.Exit(runwell.Main(version, myday.Module()))
}
```

`go get github.com/Martin-Business-Consultants/runwellgo-my-day@v0.1.0` adds it. Runwell's app (embedded in the core module) already
carries this plugin's screens.

## Developing

```sh
go vet ./... && go test ./...
golangci-lint run ./...   # v2.15.0, .golangci.yml
```

The tests run the plugin on a real database through `plugintest` (a host, a temporary
database and a stand-in for the core). To try a change in the app, check this repository out
beside runwellgo and add it to runwellgo's `go.work` (`go work use ../runwellgo-my-day`): the build
and `make plugin-ui` then use your checkout.

This code moved here from runwellgo's `plugins/my_day` and `ui/src/plugins/my_day` with its
history (runwellgo 3.1.0).

## License

[FSL-1.1-MIT](LICENSE.md), like Runwell.
