# Resources

To provide a generic way of specifying resources e.g. rpms, repositories,
source code tarballs, resource specifier is introduced.
The specification allows a user to declare a given resource without specifying
how to retrieve the resource.

Supported resources:

* github source code tarball
* user directory with source codes
* distribution rpm
* github repository
* mercurial repository

Resource specification is provided to resource client which is responsible
for retrieving specified resource. Each resource has its own provider that
retrieves the resource, e.g. from upstream repository provider
or distribution build database.

The following code shows client retrieving the resource from
resource provider located on the same host.

```python
from infra.system.resources.client import ResourceClient
from gofed_resources.proposal.providerbuilder import ProviderBuilder

client = ResourceClient(ProviderBuilder(), "/var/lib/gofed/resource_client")
client.retrieve(resource_specification)
```

The client retrieves the resource and provides location of retrieved
resource on host's filesystem. The resource location is automatically
deleted by the client (STILL NOT IMPLEMENTED!!!).

## Github source code tarball

[JSON Schema](https://github.com/gofed/infra/blob/master/system/resources/upstream_source_code_schema.json)

E.g.

```json
{
	"project": "github.com/coreos/etcd",
	"commit": "ee4ff1e44893e560535cdabd688bb23e2f6b88de",
	"resource": "upstream-source-code",
	"subresource": "directory-tree"
}
```

The specification is generated by:

```python
from infra.system.resources.specifier import ResourceSpecifier

resource_specification = ResourceSpecifier().generateUpstreamSourceCode(
	"github.com/coreos/etcd",
	"ee4ff1e44893e560535cdabd688bb23e2f6b88de"
)
```

## User directory with source codes

[JSON Schema](https://github.com/gofed/infra/blob/master/system/resources/user_directory_schema.json)

E.g.

```json
{
	"resource": "user-directory",
	"subresource": "directory-tree",
	"resource-type": "tarball",
	"location": "file:///home/user/directory/location"
}
```

The specification is generated by:

```python
from infra.system.resources.specifier import ResourceSpecifier

resource_specification = ResourceSpecifier().generateUserDirectory(
	"/home/user/directory/location"
)
```

## Distribution rpm

[JSON Schema](https://github.com/gofed/infra/blob/master/system/resources/rpm_schema.json)

E.g.

```json
{
	"product": "Fedora",
	"distribution": "rawhide",
	"build": "etcd-2.3.1-1.fc25",
	"rpm": "etcd-devel-2.3.1-1.fc25.noarch.rpm",
	"resource": "rpm",
	"resource-type": "rpm",
	"subresource": "directory-tree"
}
```

The specification is generated by:

```python
from infra.system.resources.specifier import ResourceSpecifier

resource_specification = ResourceSpecifier().generateRpm(
	"Fedora",
	"rawhide",
	"etcd-2.3.1-1.fc25",
	"etcd-devel-2.3.1-1.fc25.noarch.rpm"
)
```

## Upstream repository

[JSON Schema](https://github.com/gofed/infra/blob/master/system/resources/upstream_repository_schema.json)

E.g.

```json
{
	"repository": {
		"provider": "bitbucket",
		"username": "ww",
		"project": "goautoneg"
	},
	"resource": "repository",
	"subresource": "directory-tree"
}
```

The specification is generated by:

```python
from infra.system.resources.specifier import ResourceSpecifier

resource_specification = ResourceSpecifier().generateUpstreamRepository(
	"bitbucket",
	"ww",
	"goautoneg"
)
```
