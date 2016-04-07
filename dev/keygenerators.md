# Artefact key generators

Each artefact is assigned with a key generator.
The key is generated every time before an artefact is read from/written into an artefact storage.
Each artefact has a [list](https://github.com/gofed/infra/blob/master/generators/artefacts.json) of properties used for the key.

The key generator is primarly used for all artefact storages.

## How to add new artefact key generator

All key generators are automatically generated based on a [list of artefacts](https://github.com/gofed/infra/blob/master/generators/artefacts.json).

Each artefact has describe by the following JSON:

```json
{
        "id": "artefact-id",
        "artefact": "ARTEFACT_CONSTANT",
        "keys": ["artefact", "..."],
        "key_order": {
                "property": ["subkey1", "subkey2", "subkey3"]
        }
}
```

``key_order`` property is optional and is used for properties that consists
of other properties. At the moment, only one level property is supported.

Example for [cache-golang-project-repository-commits](https://github.com/gofed/infra/blob/master/system/artefacts/schemas/cache-golang-project-repository-commits.json) artefact.

```json
{
        "id": "cache-golang-project-repository-commits",
        "artefact": "ARTEFACT_CACHE_GOLANG_PROJECT_REPOSITORY_COMMITS",
        "keys": ["artefact", "repository"],
        "key_order": {
                "repository": ["provider", "username", "project"]
        }
},

```

Key are determined by ``artefact`` and ``repository`` properties.
As ``repository`` consists of other properties, ``key_order`` is set.

Once the ``artefacts.json`` file is updated, run ``regen.sh`` in the ``gofed/infra`` root directory.

## How to use artefact key generated

```python
from infra.system.helpers.artefactkeygenerator.keygenerator import KeyGeneratorFactory

generator = KeyGeneratorFactory().build(artefact_id)
key = generator.generate(artefact)
```

where:

* ``artefact_id`` corresponds to ``id`` property in key definition
* ``artefact`` corresponds to artefact conforming to JSON Schema for ``artefact_id`` artefacts

E.g.

```python
from infra.system.helpers.artefactkeygenerator.keygenerator import KeyGeneratorFactory

generator = KeyGeneratorFactory().build("golang-project-to-package-name")

artefact = {
	"artefact": "golang-project-to-package-name",
	"product": "Fedora",
	"distribution": "rawhide",
	"project": "github.com/coreos/etcd",
	"name": "etcd"
}

key = generator.generate(artefact)
```
