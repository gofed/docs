## Architecture

The system is aimed to analysis of projects written in Go (e.g. packaged in Fedora).
It is designed to support implementation of various tools (e.g. gofed, specker, etc.).
The system can be easily extended to other distributions and languages.

The system is designed to support:

* loosely coupled components
* reusability of components and integration in other systems
* easy extendability with new analysis and storages
* various models and analysis built over the system

The system is decomposed into two layers:

* core layer (basic components)
* model layer

Core layer consists of:

* symbol extractors
* analyzers
* symbol storages
* combinators

Extractors, analyzers and storages are introduced in a form of a plugin.
The model layer can consist of various models.
Each model can request for specific data from the core layer.
Models are not a subject of the architecture.

![System architecture](/fig/architecture_overview.png)

### Symbol extractors

Symbol extractors transforms unstrustruced data into structured data.
Unstructured data are data that are not recognized by the system.
The data must be transformed into structured first in order to be recognized by
analyzers, combinators and storages.

In general, each language has its own symbol extractors.
One language can have move extractors depending on a type of required data.
Simple extractors can for example provide basic data about dependencies
and available packages to import. Complex extractors can provide a list of
symbols from each dependency that is used in a given project.

### Analyzers

Each analyzer takes a set of structured data (and/or metadata),
runs analysis (discovery of cyclic dependencies, discovery of missing dependencies,
API breakages, etc.) and returns new structured data (and/or metadata).

Analyzers are more general and can be use among more languages.
E.g. detection of cyclic dependencies is language-free.

### Symbol storage

Storage is used to store structured data.
Storage can be represented by various specific storages, one for each data type.
The component serves to two purposes:

* provide a place to store data for analysis and extraction
* provide a database of information for users (e.g. via REST API) for additional user-specific analysis and visualization

#### Data hiearchy

Extractors provide basic data which can be used for analysis.
Product of an analysis are data which are derived from the basic data.
Derived data can be used for other analysis together with basic data.
Analysis can produce metadata as well.
These data covers summaries and other highlevel information about basic and derived data.
Metadata can be a subject to metaanalysis.

![Data hiearchy](/fig/metadata.png)

### Combinators

Some analysis may require pre-processing of input data.
Other analysis may require post-processing of output data.
Some models may require combination and reduction of structured data.
Combinators component deals with all these requirements.

### Acts

Purpose of an act is to connect all components together. Act takes responsibility for correct communication with components. E.g. picking the correct extractor and storing returned data into storage.

Act corresponds to a set of operations. E.g. Read correct data from storage, run analysis with correct form of data and store correct data into storage.

### Data representation

All data exchanged among components are encoded as JSON.
Each component defines a set of standard formats of data that are acceptable/generated.


#### Symbol extractors

**Data**

_Simple extraction example_
```vim
ProjectID:
	ImportPath: string
	Commit: string
Packages: [string]
Dependencies: map[string][string]
ExportedAPI:
	Variables: map[string]string
	Functions: map[string]string
	DataTypes: map[string]string
```

_Complex extraction example_
```vim
ProjectID:
	ImportPath: string
	Commit: string
Packages: [string]
Dependencies: map[string][string]
ExportedAPI:
	Variables: map[string]string
	Functions: map[string]string
	DataTypes: map[string]string
UsedApi:
	Projects: [ImportPath]
	Variables: map[ImportPath]map[string]string
	Functions: map[ImportPath]map[string]string
	DataTypes: map[ImportPath]map[string]string
```

Each data types is encoded as a string, e.g

```vim
type Cluster interface {
        ID() types.ID
        ClientURLs() []string
        Members() []*Member
	...
}

```

could be encoded as 

```vim
"Cluster:interface {ID() types.ID, ClientURLs() []string, Members() []*Member, ...}"
```

Precise encoding/decoding of types is language and implementation specific.

**Metadata**

```vim
ProjectID:
	ImportPath: string
	Commit: string
TestFiles: [string]
```

Each extractor specifies a set of json outputs it can provide.
Act then uses a specification to correctly transform data to be sent to a storage.

#### Analyzors

All JSON specifications are analysis related.
E.g. input for detection of cyclic dependencies:

```vim
Dependencies: map[string][string]
Commits map[string]string
```

output:

```vim
+Graph:				// definition of a data
	Nodes: [string]
	Edges: [string]		// edge = "string:string"
CyclicDependencies: [Graph]	// list of graphs
```

Distinction to data and metadata is only storage related.
Each analysis will require both data and metadata in general.

#### Combinators

The same as for analyzers.
Each combinator specifies its input and output.

#### Symbol storages

The same as for analyzers.
Each storage specifies a data that can be stored into/read from.

Act  with a help of combinators is responsible for transforming data
retrieved from an analysis/extractor to data compatible with a storage(s).
At the same time an act is not aware of any JSON specification.
Act only applies correct combinator(s).
Responsibility for correct order and use of each component is up to an act designer.
