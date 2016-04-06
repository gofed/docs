# Scan distribution build

Scan rpms of a given build in a given distribution.

## Workflow

1. Check if any of the requested rpms is stored in a database.
2. Scan each rpm that is not retrieved from the database.
3. Collect artefacts retrieved for/scanned from each rpm.
4. Provide a list of all collected artefacts.

##Input schema

* [input_schema.json](https://github.com/gofed/infra/blob/master/system/acts/scandistributionbuild/input_schema.json)

E.g.

```
{
"product": "Fedora",
"distribution": "rawhide",
"build": {
		"name": "etcd-2.3.1-1.fc25",
		"rpms": [
			"etcd-devel-2.3.1-1.fc25.noarch.rpm",
			"etcd-unit-test-2.3.1-1.fc25.armv7hl.rpm",
			"etcd-unit-test-2.3.1-1.fc25.i686.rpm",
			"etcd-unit-test-2.3.1-1.fc25.x86_64.rpm"
		]
	}
}
```

##Provided artefacts

* [ARTEFACT_GOLANG_PROJECT_DISTRIBUTION_EXPORTED_API](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-distribution-exported-api.json)
* [ARTEFACT_GOLANG_PROJECT_DISTRIBUTION_PACKAGES](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-distribution-packages.json)
* [ARTEFACT_GOLANG_IPPREFIX_TO_RPM](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-ipprefix-to-rpm.json)

## Usage

```python
from infra.system.core.factory.actfactory import ActFactory

act = ActFactory().bake("scan-distribution-build")
data = {
	"product": "Fedora",
	"distribution": "rawhide",
	"build": {
			"name": "etcd-2.3.1-1.fc25",
			"rpms": [ "etcd-devel-2.3.1-1.fc25.noarch.rpm" ]
		}
	}
}

artefacts = act.call(data)
```
