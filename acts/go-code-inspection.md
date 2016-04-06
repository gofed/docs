# Go code inspection

Scan upstream project (or user directory) and provide basic information about its source code.
E.g. list of defined packages, list of dependencies, list of tests, etc.

## Workflow

1. If upstream project is specified check if it is scanned.
2. If the upstream project is not scanned or the user directory is specified, scan.
3. Provide artefact describing basic information about the source code.

##Input schema

* [input_schema.json](https://github.com/gofed/infra/blob/master/system/acts/gocodeinspection/input_schema.json)

E.g.

```
{
	"type": "user_directory",
	"resource": "/home/user/goproject",
	"ipprefix": "github.com/golang/net" 
}
```

##Provided artefacts

* [ARTEFACT_GOLANG_PROJECT_PACKAGES](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-packages.json)

## Usage

```python
from infra.system.core.factory.actfactory import ActFactory

act = ActFactory().bake("go-code-inspection")
data = {
	"type": "user_directory",
	"resource": "/home/user/goproject",
	"ipprefix": "github.com/golang/net" 
}

artefacts = act.call(data)
```
