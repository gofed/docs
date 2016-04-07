# Extended lint proposal

## Currently checked

- list of provided packages
- list of dependencies (BuildRequires, Requires)
- package name

## Proposal

### list of main packages

- most likely define new macro ``mains`` containing a list of directories of files with ``package main`` directive
- lint will use the list to check for deps of main packages
- e.g.

```
%global mains ". cmd/hyperkube cmd/kubectl cmd/apiserver"
```

### partial bundled deps

- new macro ``bundled_deps`` will be introduced
- user will be able to specify which deps will be installed from debundled and which from bundled deps
- only for devel subpackage/subpackages
- e.g.

```vim
%if 0%{?bundled_deps}
Provides: bundled(golang(path1))
Provides: bundled(golang(path2))
Provides: bundled(golang(path3))
Provides: bundled(golang(path4))
%else
BuildRequires: bundled(golang(path1))
BuildRequires: bundled(golang(path2))
BuildRequires: bundled(golang(path3))
BuildRequires: bundled(golang(path4))
%endif

BuildRequires: bundled(golang(path5))
BuildRequires: bundled(golang(path6))
```

- lint will use it to check if all dependencies are listed

### devel subpackage decomposition

- devel subpackage can be decomposed into more subpackages or smaller size
- aim here is to provide smaller dependency trees
- each subpackage can have its own partial bundled deps (so the same ``bundled_deps`` apply here)
- lint will check if the decomposition cover all packages and deps in the entire devel subpackage

### back-compatibility devel subpackages

- some packages can ship the same devel source just with different import path prefix
- lint will check all devel subpackages
- some packages can ship more devel subpackages from different tarballs
- lint will check all devel subpackages (one per tarball)
- some packages can ship combination of both (different import path prefix, different tarballs)
- this will require definition of new macros and implicit behavior
