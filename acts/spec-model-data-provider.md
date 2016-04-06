# Spec model data provider

Collect all data needed/suitable for construction of program specification model
for upstream project or for user specified directory with go source code.

## Workflow

1. If an upstream project check if it is stored in a database.
2. If the upstream project is not in the database or if a user directory is specified,
   extract golang-project-packages and golang-project-content-metadata artefacts
3. Provide both artefacts

##Input schema

* [input_schema.json](https://github.com/gofed/infra/blob/master/system/acts/specmodeldataprovider/input_schema.json)

E.g.

```
{ 
	"type": "user_directory",
	"resource": "/home/user/sourcecodedirectory",
	"ipprefix": :github.com/myname/myproject"
}
```

or

```
data = {
	"type": "upstream_source_code",
	"project": "github.com/golang/net",
	"commit": "af4fee9d05b66edc24197d189e6118f8ebce8c2b",
	"ipprefix": "golang.org/x/net"
}
```

##Provided artefacts

* [ARTEFACT_GOLANG_PROJECT_PACKAGES](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-packages.json)
* [ARTEFACT_GOLANG_PROJECT_CONTENT_METADATA](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-content-metadata.json)

## Usage

```python
from infra.system.core.factory.actfactory import ActFactory

act = ActFactory().bake("spec-model-data-provider")
data = {
	"type": "upstream_source_code",
	"project": "github.com/golang/net",
	"commit": "af4fee9d05b66edc24197d189e6118f8ebce8c2b",
	"ipprefix": "golang.org/x/net"
}

artefacts = act.call(data)
```
