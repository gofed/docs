## Acts definitions (how it could be)

### Spec model data provider

Summary of steps:

* input: project and commit
* check if the project is already packages in Fedora
* check if the project is already analyzed (available artefacts)
* analyze source codes form tarball in necessary (optionally store artefacts into client local storage)
* check if all its deps are already packages in Fedora (check devel, main packages,tests)
* return all data valid for spec model

Generating the final spec file is left for the model layer (e.g. Specker).
Model can require more data than the core layer can provide.
What is suitable for a spec model is choosen with the best effort.

Required artefacts:

* golang-project-info-fedora
* golang-project-packages
* golang-project-to-package-name

User can provide arbitrary tarball.
In that case no check for presence in PkgDb, no storing of extracted data into a storage.
Only check for presence of dependencies.
However, the input JSON/Schema is different.
There is no `project` not `commit` key.
Just location of a tarball (or its directory).
Both ways are possible.

```vim
Input: project, commit

# Check if the given project is already in PkgDB
storage = FunctionFactory(SIMPLE_STORAGE).build()
data = {"request": "read", "artefact": "golang-project-info-fedora", "project": project}
data = storage.call(data)
# empty data or not? if not empty continue if specified

# check if the project and commit are already extracted
data = {"request": "read", "artefact": "golang-project-packages", "project": project, "commit": commit}
data = storage.call(data)
# if not or the storage was not able to retrieve the data, process the tarball

# get tarball
tarball = downloadTarball() # use TarballStorage for it
directory = tarballExtractor(tarball) # use DirectoryStorage for it

# Construct data
data = {"project": ..., "commit": ..., "source_code_directory": directory}

# Get extractor and extract data
extractor = FunctionFactory(GOEXTRACTOR).build()
data = extractor.call(data)
# Check for errors
...

# Send data to a storage (can be voluntary).
# It can be stored to local storage of a client.
# Spec model data provider is not a good place for extracting data
# from source code. This act is not meant to store data into global server storage.
storage = FunctionFactory(SIMPLE_STORAGE).build()
data = {"request": "store", "data": data}
data = storage.call(data)
# Check for errors

# Checkout each dependency if it is packaged in Fedora
for each deps:
	data = {"request": "read", "artefact": "golang-project-info-fedora", "project": deps[project], "commit": deps[commit]}
	storage.call(data)
# all deps can be sent as one request to the storage
# to check if the correct commit is packages is not subject of the act
# check of availability/suitability of all deps is a subject of different act

# Report check of all dependencies
# There are three areas to check for dependences: devel, main packages, tests
# Main packages does not have to be checked if the build section of spec file is empty. In the end, it is still check for a presence of deps in the storage.
# Some deps can be common. So send all deps at once and then just distrubed presence flag to each area.

# MODEL LAYER
# Models should not be in any act. Model should communicate and retrieve data through core layer API. Still needs to be discussed. Keeping the model here for now.

# Get data from artefacts in a form which spec file generator understands.
data = modify(data) # still too much pseudo
# Throw data to a model
# This does not belong here so let us just state it for future reference
model = ModelFactory(SPECKER).build()
data = model.call(data)

# Get a suitable package name for the project
# The script will automatically generated a name a check if it is not already used

# data contains a JSON with generated spec file
# write the spec file into a file
```

### Extract data from source code

Special case of "Spec file generator" act. In this case all artefacts are always saved into a storage.

### Apidiff

Summary of steps:

* get exported api for both commits (if any one is missing, export the data from downloaded tarball)
* compute apidiff
* store the apidiff into a storage
* return the apidiff

Required artefacts:

* golang-project-exported-api
* golang-project-api-diff

This act is not distribution specific.
It is run over upstream repositories.
It is possible to specify local tarball instead of commit(s).
In that case extracted data from the tarball and following apidiff are not stored.

```vim
Input: project, commit1, commit2

# Retrieve data about (project, commit1) and (project, commit2)
storage = FunctionFactory(SIMPLE_STORAGE).build()
data0 = [{"request": "read", "artefact": "golang-project-exported-api", "project": project, "commit": commit1}, {"request": "read", "artefact": "golang-project-exported-api", "project": project, "commit": commit2}]
data = storage.call(data)
# check for errors

# if commit1 can not be found, try to download it from upstream and extract
# get tarball
tarball = downloadTarball() # use TarballStorage for it
directory = tarballExtractor(tarball) # use DirectoryStorage for it

# Construct data
data = {"project": project, "commit": commit1, "source_code_directory": directory}

# Get extractor and extract data
extractor = FunctionFactory(GOEXTRACTOR).build()
data1 = extractor.call(data)
# Check for errors
...

# Send data to a storage (can be voluntary)
storage = FunctionFactory(SIMPLE_STORAGE).build()
data = {"request": "store", "data": data1}
data = storage.call(data)
# Check for errors

# if commit2 can not be found, try to download it from upstream and extract
# get tarball
tarball = downloadTarball() # use TarballStorage for it
directory = tarballExtractor(tarball) # use DirectoryStorage for it

# Construct data
data = {"project": project, "commit": commit2, "source_code_directory": directory}

# Get extractor and extract data
extractor = FunctionFactory(GOEXTRACTOR).build()
data2 = extractor.call(data)
# Check for errors
...

# Send data to a storage (can be voluntary)
storage = FunctionFactory(SIMPLE_STORAGE).build()
data = {"request": "store", "data": data2}
data = storage.call(data)
# Check for errors

# Combine data0, data1 and data2 into data to provide data for analysis
analysis = FunctionFactory(APIDIFF).build()
data = analysis.call(data)
# Check for errors

# Safe data to storage
data = {"request": "store", "data": data}
data = storage.call(data)
# Check for errors

# Return the data from the act
return data
```

### Check commit

Input: project, commit

Summary of steps:

* check if a project of a given commit is already packaged in Fedora
* if so get packaged commit
* if both commits are the same, return up-to-date
* if not, get dates of both commits and compare them

Required artefacts:

* golang-project-info-fedora
* golang-project-repository-commit
* golang-project-repository-info

```vim
Input: project, commit

# Check if the given project is already in PkgDB and if it is up-to-date or not
storage = FunctionFactory(SIMPLE_STORAGE).build()
data = {"request": "read", "artefact": "golang-project-info-fedora", "project": project}
data = storage.call(data)
# if empty data, return empty data

# From the data, retrieve commit. If both commits are the same, return up-to-date.
# If not, retrieve info about each commit from upstream repository.
# Retrieve info about client specified commit (commit1) and fedora retrieved one (commit2)
data = [{"request": "read", "artefact": "golang-project-repository-commit", "project": project, "commit": commit1}, {"request": "read", "artefact": "golang-project-repository-commit", "project": project, "commit": commit2}]
data = storage.call(data)

# If upstream commit is missing (not foud), try to retrieve missing data
# Get info about repository
data = {"request": "read", "artefact": "golang-project-repository-info", "project", project}
data = storage.call(data)
# Check for errors
# Get repository 

# Retrieve data for commit2
analyzer = FunctionFactory(COMMIT_ANALYZER).build()
data = {"repository": repository, "commit": commit2}
data = analyzer.call(data)
# Check for errors

# Or better: send a message to a server that "missing commit for repository".
# The act returns "upstream commit not foud"
# Server can have triggers that periodically runs analyses for missing data

# Compare both dates and return outdated, up-to-date, newer or not-yet-processed
```

### Check deps

The same as for `Check commit`. It is called for each commit separately.

### Tarball inspection

It is used for anonymous source codes (client does not specify project and commit).

Data are extracted from the source code as for spec file generator.
Requested data are then sent back (e.g. list od dependencies, list of tests, list of provided packages, list of main packages)

### Spec file review

It can be decomposed into two areas:

* general steps (build prms, run rpmlint, make Koji build, upload spec and srpm to a public ftp server, generate request in BZ)
* golang specific steps (run gofed lint)

Spec file review is built over core layer, not part of it.
