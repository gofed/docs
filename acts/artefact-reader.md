# Artefact reader

Retrieve artefact specified by a key.

## Workflow

1. Check if a given artefact for a given key exists
2. If the key exists, return the artefact
3. If the key does not exist, fail.

##Input schema

* [input_schema.json](https://github.com/gofed/infra/blob/master/system/acts/artefactreader/input_schema.json)

E.g.

```
{
	"artefact": "golang-distribution-snapshot"
	"distribution": {
		"product": "Fedora",
		"version": "rawhide"
	}
}
```

## Retriveable artefacts

* [ARTEFACT_GOLANG_IPPREFIX_TO_RPM](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-ipprefix-to-rpm.json)
* [ARTEFACT_GOLANG_PROJECT_DISTRIBUTION_PACKAGES](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-distribution-packages.json)
* [ARTEFACT_GOLANG_DISTRIBUTION_SNAPSHOT](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-distribution-snapshot.json)

## Usage

```python
from infra.system.core.factory.actfactory import ActFactory

act = ActFactory().bake("artefact-reader")
data = {
	"artefact": "golang-distribution-snapshot"
	"distribution": {
		"product": "Fedora",
		"version": "rawhide"
	}
}

artefacts = act.call(data)
```
