## Request for data

Request can be divided into two categories:

* request for data retrieval
* request for data storage

In order to provide required data for various analyzors and other workers,
storage query language is introduced. When requesting for data retrieval,
query is parsed and data corresponding to the query returned
(as a list of individual [artefacts](data_specification.md#artefacts)).

When requesting for data storage, all data artefacts are validated first.
Each artefact is described by a JSON Schema.
Any artefact that is not valid is discarded.

![Storage handler](/fig/storage_handler.png)

### Storage query language

**TODO**

E.g. get a symbol table for _github.com/coreos/pkg/capnslog_ with _2c77715c4df99b5420ffcae14ead08f52104065d_ commit.

```vim
"artefact = golang-project-symbol-table AND project = 'github.com/coreos/pkg/capnslog' AND commit = '2c77715c4df99b5420ffcae14ead08f52104065d'"
```

**project** and **commit** are artefact specific fields so maybe use
**artefact.project** and **artefact.commit** and if those fields does not exist,
query is not valid.
In that case everything other than artefact keyword is a field of the artefact.

### Storages

All storages are JSON storages. Key-value databases should be sufficiend, e.g. [etcd](http://github.com/coreos/etcd).

### Conclusion

This way any worker can request for an arbitrary list of artefacts and return an arbitrary list of artefacts as long as all JSON schemas are fulfilled and all artefacts defined.

In general, each artefact is searchable by its name and a list of attributes
specifing concrete datum. E.g. for a symbol table it is artefact, project and commit.
Corresponding key is a triple ('golang-project-symbol-table','github.com/coreos/pkg/capnslog','2c77715c4df99b5420ffcae14ead08f52104065d').
