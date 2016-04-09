# Scan distribution package

For a given distribution package get a list of all builds in a given range
and provide basic data about the repository itself and each scanned build.

## Workflow

1. Check if the requested distribution package within the given range is scanned.
2. If the range is not fully covered scan basic data about missing builds.
3. Provide artefacts describing basic information about the package
   and data about builds on a given range.

##Input schema

* [input_schema.json](https://github.com/gofed/infra/blob/master/system/acts/scandistributionpackage/input_schema.json)

E.g.

```
{
	"package": "etcd",
	"product": "Fedora",
	"distribution": "f24",
	"start_timestamp": 1436561736,
	"end_timestamp": 1449492889,
}
```

##Provided artefacts

* [ARTEFACT_GOLANG_PROJECT_DISTRIBUTION_PACKAGE_BUILDS](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-distribution-package-builds.json)
* [ARTEFACT_GOLANG_PROJECT_DISTRIBUTION_BUILD](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-distribution-build.json)

## Usage

```python
from infra.system.core.factory.actfactory import ActFactory

act = ActFactory().bake("scan-distribution-package")
data = {
	"package": "etcd",
	"product": "Fedora",
	"distribution": "f24",
	"start_timestamp": 1436561736,
	"end_timestamp": 1449492889,
}

artefacts = act.call(data)
```
