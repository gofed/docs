## Acts definitions

### Spec file generator

Summary of steps:

* input: project and commit
* check if the project is already packages in Fedora
* check if the project is already analyzed (available artefacts)
* analyze source codes form tarball in necessary (and store them)
* check if all its deps are already packages in Fedora (check devel, main packages,tests)
* send data to specker to render spec file (here for the moment, should be moved to model layer)

Required artefacts:

* golang-project-info-fedora
* golang-project-packages

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

# Send data to a storage (can be voluntary)
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
model = ModelFactory(SPECKER).build()
data = model.call(data)

# data contains a JSON with generated spec file
# write the spec file into a file
```

