# Scan upstream repository

For a given upstream repository scan all commits in a given range
and provide basic data about the repository itself.
Optionally, retrieve data for a single commit instead of range.

## Workflow

1. Check if the requested upstream repository within the given range is scanned.
2. If the range is not fully covered scan missing commits.
3. Provide artefacts describing basic information about the repository
   and data about commits on a given range.

Or

1. Check if a single commit for the requested upstream repository is scanned.
2. If it is not extract data about the commit from the repository.
3. Provide artefact describing the single commit.

##Input schema

* [input_schema.json](https://github.com/gofed/infra/blob/master/system/acts/scanupstreamrepository/input_schema.json)

E.g.

```
{
	"repository": "github.com/coreos/etcd",
	"start_timestamp": 1459671553,
	"end_timestamp": 1459971553
}
```

##Provided artefacts

* [ARTEFACT_GOLANG_PROJECT_REPOSITORY_INFO](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-repository-info.json)
* [ARTEFACT_GOLANG_PROJECT_REPOSITORY_COMMIT](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/golang-project-repository-commit.json)

## Usage

```python
from infra.system.core.factory.actfactory import ActFactory

act = ActFactory().bake("scan-upstream-repository")
data = {
	"repository": "github.com/coreos/etcd",
	"start_timestamp": 1459671553,
	"end_timestamp": 1459971553
}

artefacts = act.call(data)
```
