## Architecture

The system is aimed to analysis of projects written in Go and packaged in Fedora.
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
* transformators
* client

The model layer can consist of various models.
Each model can request for specific data from the core layer.
Models are not a subject of the architecture.

### Symbol extractors

Symbol extractors transforms unstrustruced data into structured data.
Unstructured data are date that are not recognized by the system.
The data must be first transformed into structured that are recognized by
analyzers, transformators and storages.

In general, each language has its own symbol extractors.
One language can have move extractors depending on a type of required data.
Simple extractors can for example provide basic data about dependencies
and available packages to import. Complex extractors can provide a list of
symbols from each dependency that is used in a given project.
