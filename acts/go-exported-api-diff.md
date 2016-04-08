# Go exported api diff

Compare exported API of two golang projects.

## Workflow

1. Extract/Retrieve exported api of reference project
2. Extract/Retrieve exported api of project to be compared with reference project
3. Compare both projects
4. Provide difference in API (reference project from step 1. - compared with project from step 2.)

##Input schema

* [input_schema.json](https://github.com/gofed/infra/blob/master/system/acts/goexportedapidiff/input_schema.json)

E.g.

```
{
	"reference": {
		"type": "upstream_source_code",
		"project": "github.com/bradfitz/http2",
		"commit": "f8202bc903bda493ebba4aa54922d78430c2c42f",
		"ipprefix": "github.com/bradfitz/http1"
	},
	"compared_with": {
		"type": "upstream_source_code",
		"project": "github.com/bradfitz/http2",
		"commit": "953b51136f12cb27503bec0f659432fd1fa97770",
		"ipprefix": "github.com/bradfitz/http1"
	}
}
```

##Provided artefacts

* [ARTEFACT_GOLANG_PROJECTS_API_DIFF](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-projects-api-diff.json)

## Usage

```python
from infra.system.core.factory.actfactory import ActFactory

act = ActFactory().bake("go-exported-api-diff")
data = {
	"reference": {
		"type": "upstream_source_code",
		"project": "github.com/bradfitz/http2",
		"commit": "f8202bc903bda493ebba4aa54922d78430c2c42f",
		"ipprefix": "github.com/bradfitz/http1"
	},
	"compared_with": {
		"type": "upstream_source_code",
		"project": "github.com/bradfitz/http2",
		"commit": "953b51136f12cb27503bec0f659432fd1fa97770",
		"ipprefix": "github.com/bradfitz/http1"
	}
}

artefacts = act.call(data)
```
