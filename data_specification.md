## Data specification

### Some interesting data

#### project scoped

* list of commits in upstream repository
* list of releases, list of tags
* list of defined packages
* list of dependencies (for each package, only for devel)
* list of tests (for each package)
* list of main packages (with a dependency list for each)
* exported API (data types, consts, variables, functions (normal or receivers))
* commit packaged in Fedora (or a list of commits)
* commit packaged in a distribution (in general)
* dependency graph (or project's packages)
* list of used symbols of dependencies (changing from commit to commit)
* change of rate of project's dependencies (time progress graph)

#### inter-project scoped

* dependencies graph (of a subset of projects, e.g. docker, etcd)
* api diff (of 2 projects/commits)
* similarity of two projects

#### distribution scoped

* dependency graph (changing in time)
* list of (commit, import path) parts for each project packaged in Fedora (distribution)
* user API (for each project a list of other projects that use its API with a list of symbols for the API)
* global glossary of exported symbols for each project (per date/commit) or for API currently in a distro (rawhide, other distributions)
* list of releases/commits packaged in a distribution
* mapping of projects to package name (spec)

And other data that will show up later.

#### notes

* Some data can be cached for faster lookup (e.g. list of pairs (commit, import path), etc.)
* Some data can be computed/constructed from other data (no need to store them)
* Some data can be historical (e.g. list of commits previously in a distribution, etc.)

### Artefacts

#### artefact: list of defined packages
```yaml
artefact: 'golang-project-packages'
project: 'github.com/coreos/etcd'
commit: '729b530c489a73532843e664ae9c6db5c686d314'
ipprefix: 'github.com/coreos/etcd'
data:
  packages: "etcdctlv3/command,etcdmain,etcdserver,etcdserver/api/v3rpc,..."
  dependencies:
    'etcdctlv3/command': 'github.com/bgentry/speakeasy,github.com/codegangsta/cli,...'
    'etcdmain': 'github.com/coreos/etcd/discovery,github.com/coreos/etcd/etcdserver,...'
    ...
  tests: "storage/backend,storage,..."
  main: "main.go,etcdctl/main.go,tools/v3benchmark/report.go,..."
```

#### artefact: project info
```yaml
artefact: 'golang-project-info'
project: 'github.com/coreos/etcd'
ipprefix: 'github.com/coreos/etcd'
provider-prefix: 'github.com/coreos/etcd'
releases: "v2.2.2,v2.2.1,..."
tags: "..."
```

#### artefact: project info commits
```yaml
artefact: 'golang-project-info-commit'
project: 'github.com/coreos/etcd'
commits:
  - 'hash': 'b4bddf685b26b4aa70e939445044bdeac822d042'
    'date': '2015-11-19'
    'extra': '...'
  - 'hash': ...
  ...
```

#### artefact: exported API
```yaml
artefact: 'golang-project-exported-api'
project: 'github.com/coreos/etcd'
commit: '729b530c489a73532843e664ae9c6db5c686d314'
packages:
 - 'package': 'pkg/cors'
   'datatypes':
   - 'CORSInfo map[string]bool;Set,String,OriginAllowed'
   - 'CORSHandler struct {Handler http.Handler,Info *CORSInfo};ServeHTTP'
   'functions':
   - '(ci *CORSInfo) Set(s string) error'
   - '(ci *CORSInfo) String() string'
   - '(c CORSInfo) OriginAllowed(origin string) bool'
   - '(h *CORSHandler) ServeHTTP(w http.ResponseWriter, req *http.Request)'
   'vars':
   - ...
   'consts':
   - ...
 - 'package': 'pkg/wait'
   'datatypes':
   - ...
   ...
```

#### artifact: distribution commit
```yaml
artefact: 'golang-project-info-fedora'
project: 'github.com/coreos/etcd'
commit: '729b530c489a73532843e664ae9c6db5c686d314'
last-updated: '2015-12-12'
```

Other fields can be added later.

#### artefact: list of used symbols
```yaml
artefact: 'golang-project-allocated-api'
project: 'github.com/coreos/etcd'
commit: '729b530c489a73532843e664ae9c6db5c686d314'
packages:
 - 'package': 'pkg/cors'
   'symbols': 'string,bool,error,strings.Split,strings.TrimSpace,...'
```

#### artefact: api diff (pr2 - pr1)
```yaml
artefact: 'golang-projects-api-diff'
commits: 'commit1,commit2'
projects: 'project1,project2'
difference:
  new-packages: '...'
  removed-packages: '...'
  updated-packages:
  - 'package': '...'
    new-types:
    - "..."
    removed-types:
    - "..."
    updated-types:
    - "..."
    new-functions:
    - "..."
    removed-functions:
    - "..."
    updated-functions:
    - "..."
    new-vars:
    - "..."
    removed-vars:
    - "..."
    updated-vars:
    - "..."
    new-consts:
    - "..."
    removed-consts:
    - "..."
    updated-consts:
    - "..."
  - 'package': '...'
    ...
  ...
   
```

### artefact: project to package name mapping
```yaml
artefact: golang-project-to-package-name
project: 'github.com/coreos/etcd'
name:
'fedora': 'etcd'
'rhel': 'etcd'
'???': '???'
```
