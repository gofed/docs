# Resources

## Resource categories

### User provided resources

The mostly provided user resource is a directory.
The directory can contain source codes, spec file, repository or others.
Some analysis run locally, some can run remotelly.
If a user wants to run remote analysis,
provided directory has to be uploaded to remote storage.

### Upstream provided resources
The mostly provided upstream resource is tarball with project source codes.
Each tarball is uniquely determined by repository, project and commit.
Entire repository for cross-commit analysis can be provided as well.

### Distribution provided resources
Each distribution has its own process how to introduce a golang project.
Projects can be contain in packages. Each package can provide a set of builds.
Each build provides one or more (s)rpms on each a given analysis can be run.

## Resource manipulation

### Specifier

In order to provide an abstract way for specifying required resources,
resource specifier is introduced.
Each resource can provide various subresources such as spec file or
directory tree.
On the other end, a processor expect local path to required resource.
Thus, it is convenient to hide manipulation with resources.
Before a data are sent to a function, only required resource is specified.
Once the data get to a processor, only location of a resource is required.

### Providers

TODO: Clients, Storages

## Resource delivery

### User defined resources

Act is not responsible for retrieving or storing resources from a user.
It is up to a client code to provide local or remote location of a resource.
When a user wants to run remote analysis, it is up to a client tool to upload
a resource to accessible location where it can be retrieved by the remote infrastructure.
If it is a remote analysis over local directory (in which client tool will could archive
the directory, upload it to fedorapeople.org portal and forwars link to the tarball),
or remote analysis over local srpm (in which client tool will upload the srpm to remote
infrastructure storage and receives unique identifier),
it is up to a client tool to ensure a given resource is retrievable.

### Upstream provided resources
Some analysis may require directory tree with a source codes
for a given project and commit.
Source code can be retrieved from repository via https as a tarball.
Or it could be generated from locally cloned repository.
Or it can be retrieved from a local cache.
What is the best way (or more efficient) it up to individual resource providers.

### Distribution provided resources
The same as for upstream provided resources holds here.
Each distribution can alter a given golang project.
Source codes can be delivered via rpm builds.
Thus each distribution requires distribution specific provides.
