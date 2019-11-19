[![CircleCI](https://circleci.com/gh/eclipse/che-plugin-broker.svg?style=svg)](https://circleci.com/gh/eclipse/che-plugin-broker)

[![codecov](https://codecov.io/gh/eclipse/che-plugin-broker/branch/master/graph/badge.svg)](https://codecov.io/gh/eclipse/che-plugin-broker)

# This repo contains implementations of several Che plugin brokers

## artifacts-plugin-broker

This broker runs as an init container on the workspace pod. Its job is to take in a list of plugin identifiers (either references to a plugin in the registry or a link to a plugin meta.yaml) and ensure that the correct .vsix and .theia extenions are downloaded into the `/plugins` directory, for each plugin requested for the workspace.

## metadata-plugin-broker

This broker must be run prior to starting the workspace's pod, as its job is to provision required containers, volumes, and environment variables for the workspace to be able to start with the installed plugins enabled.

## Development

Mocks are generated from interfaces using library [mockery](https://github.com/vektra/mockery)
To add new mock implementation for an interface or regenerate to an existing one use following
command when current dir is location of the folder containing the interface:

```shell
mockery -name=NameOfAnInterfaceToMock
```

### Build

There is a Makefile included in the repo to make building and testing the code easier:

| make target | function |
| --- | --- |
| `make ci` | Run CI tests in docker |
| `make build` | Build all code |
| `make build-artifacts` | Build only the artifacts broker, as binary `plugin-artifacts-broker` in the root of this repo |
| `make build-metadata` | Build only the metadata broker, as binary `plugin-metadata-broekr` in the root of this repo |
| `make test` | Run all tests in repo |
| `make lint` | Run `golangci-lint` on repo |
| `make fmt` | Run `go fmt` on all `.go` files |
| `make dep-update` | Run `dep ensure`; must be run after updating dependencies |
| `make build-docker-artifacts` | Build `eclipse/che-plugin-artifacts-broker` image |
| `make build-docker-metadata` | Build `eclipse/che-plugin-metadata-broker` image |
| `test-metadata` | Build and run metadata broker locally, using plugin ids from `brokers/testdata/config-plugin-ids.json`; prints output to stdout |
| `test-artifacts` | Build and run artifacts broker locally, using plugin ids from `brokers/testdata/config-plugin-ids.json`; downloads all extensions to `/plugins` locally (directory must be writable, e.g. via a softlink to a user-writable directory) |

For more information, view the targets in the Makefile.

### Dependencies

Dependencies in the project are managed by Go Dep.
After you added a dependency you need to run the following command to download dependencies to vendor repo and lock file and then commit changes:

```shell
dep ensure
```

`dep ensure` doesn't automatically change Gopkg.toml which contains dependencies constrants.
So, when a dependency is introduced or changed it should be reflected in Gopkg.toml.
