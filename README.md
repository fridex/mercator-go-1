Mercator 2.0
============

Mercator is a Swiss army knife for obtaining package metadata across various package ecosystems:

| Language | Ecosystem |
|----------|-----------|
| Python   | PyPI |
| Ruby     | Gems |
| Node     | NPM |
| Java     | Maven |
| Rust     | Cargo |
| .NET     | Nuget |
| Haskell  | Hackage |

Simply point Mercator at some directory and it will walk down all child directories and collect information
about all encountered package manifests. The output is always a JSON document describing what has been
found, please note that the key/value layout of the JSON document depends on the package ecosystem
that produced it, so if you want to do some further processing or analytics you may want to [normalize the data](https://github.com/fabric8-analytics/fabric8-analytics-worker/blob/master/f8a_worker/data_normalizer.py).

## Contributing

See our [contributing guidelines](https://github.com/fabric8-analytics/common/blob/master/CONTRIBUTING.md) for more info.
 
## Installation

Necessary dependencies (package names are taken from Fedora) to build Mercator without any handlers:

```
openssl-devel git golang make
```

Per handler dependencies:

Ruby:

```
ruby
```

Java:

```
java-devel maven
```

Python:

```
python3 python3-devel
```

Javascript:

```
nodejs
```

Dotnet:

```
mono-devel nuget
```

Golang:

```
glide
```

for Dotnet you have to execute this command first:

```
yes | certmgr -ssl https://go.microsoft.com && yes | certmgr -ssl https://nuget.org
```

All handler dependencies together:
```
ruby java-devel python3 python3-devel nodejs mono-devel nuget glide
```

If you have all the packages installed, make sure that your `GOPATH` is set.
You can set it to for example `$(pwd)` or `/tmp` like: `export GOPATH=/tmp`.

Then just invoke `make`:

```
make build
sudo make install
```

Some handlers are built/installed by default, some need to be explicitly enabled,
see beginning of [Makefile](Makefile).

If you need to build for example dotnet handler (which is disabled by default),
you either have to change `DOTNET=NO` to `DOTNET=YES` in [Makefile](Makefile) or
build it like:

```
make build DOTNET=YES
```

Note: You can also take a look at our [spec file](mercator.spec), which we use to build RPMs.

## Running

After that, `Mercator` is ready to be used:

```
$ mercator jsl/
[
   {
      "path": "/home/podvody/Repos/jsl/setup.py",
      "ecosystem": "Python",
      "result": {
         "author": "Anton Romanovich",
         "author_email": "anthony.romanovich@gmail.com",
         "classifiers": [
            "Development Status :: 4 - Beta",
            "Intended Audience :: Developers",
            "License :: OSI Approved :: BSD License",
            "Operating System :: OS Independent",
            "Programming Language :: Python",
            "Programming Language :: Python :: 2",
            "Programming Language :: Python :: 2.6",
            "Programming Language :: Python :: 2.7",
            "Programming Language :: Python :: 3",
            "Programming Language :: Python :: 3.3",
            "Programming Language :: Python :: 3.4",
            "Programming Language :: Python :: Implementation :: CPython",
            "Programming Language :: Python :: Implementation :: PyPy",
            "Topic :: Software Development :: Libraries :: Python Modules"
         ],
         "description": "A Python DSL for defining JSON schemas",
         "ext_modules": [],
         "license": "BSD",
         "long_description": "JSL\n===\n\n.. image:: https://travis-ci.org/aromanovich/jsl.svg?branch=master\n    :target: https://travis-ci.org/aromanovich/jsl\n    :alt: Build Status\n\n.. image:: https://coveralls.io/repos/aromanovich/jsl/badge.svg?branch=master\n    :target: https://coveralls.io/r/aromanovich/jsl?branch=master\n    :alt: Coverage\n\n.. image:: https://readthedocs.org/projects/jsl/badge/?version=latest\n    :target: https://readthedocs.org/projects/jsl/\n    :alt: Documentation\n\n.. image:: http://img.shields.io/pypi/v/jsl.svg\n    :target: https://pypi.python.org/pypi/jsl\n    :alt: PyPI Version\n\n.. image:: http://img.shields.io/pypi/dm/jsl.svg\n    :target: https://pypi.python.org/pypi/jsl\n    :alt: PyPI Downloads\n\nDocumentation_ | GitHub_ |  PyPI_\n\nJSL is a Python DSL for defining JSON Schemas.\n\nExample\n-------\n\n::\n\n    import jsl\n\n    class Entry(jsl.Document):\n        name = jsl.StringField(required=True)\n\n    class File(Entry):\n        content = jsl.StringField(required=True)\n\n    class Directory(Entry):\n        content = jsl.ArrayField(jsl.OneOfField([\n            jsl.DocumentField(File, as_ref=True),\n            jsl.DocumentField(jsl.RECURSIVE_REFERENCE_CONSTANT)\n        ]), required=True)\n\n``Directory.get_schema(ordered=True)`` will return the following JSON schema:\n\n::\n\n    {\n        \"$schema\": \"http://json-schema.org/draft-04/schema#\",\n        \"definitions\": {\n            \"directory\": {\n                \"type\": \"object\",\n                \"properties\": {\n                    \"name\": {\"type\": \"string\"},\n                    \"content\": {\n                        \"type\": \"array\",\n                        \"items\": {\n                            \"oneOf\": [\n                                {\"$ref\": \"#/definitions/file\"},\n                                {\"$ref\": \"#/definitions/directory\"}\n                            ]\n                        }\n                    }\n                },\n                \"required\": [\"name\", \"content\"],\n                \"additionalProperties\": false\n            },\n            \"file\": {\n                \"type\": \"object\",\n                \"properties\": {\n                    \"name\": {\"type\": \"string\"},\n                    \"content\": {\"type\": \"string\"}\n                },\n                \"required\": [\"name\", \"content\"],\n                \"additionalProperties\": false\n            }\n        },\n        \"$ref\": \"#/definitions/directory\"\n    }\n\nInstalling\n----------\n\n::\n\n    pip install jsl\n\nLicense\n-------\n\n`BSD license`_\n\n.. _Documentation: http://jsl.readthedocs.org/\n.. _GitHub: https://github.com/aromanovich/jsl\n.. _PyPI: https://pypi.python.org/pypi/jsl\n.. _BSD license: https://github.com/aromanovich/jsl/blob/master/LICENSE\n",
         "name": "jsl",
         "packages": [
            "jsl",
            "jsl.fields",
            "jsl._compat"
         ],
         "url": "https://jsl.readthedocs.org",
         "version": "0.2.1"
      }
   }
]
```

## Tests
For starting tests you have to execute:
```
make check
```

## Principles

Mercator 1.0 was written mostly in Python, while Python can be considered a ubiquity in certain circles, less so in other circles. The main principle and reason for rewrite was to accomodate for the cases where Python was not installed and installing it didn't make any sense. Expecting a Java dev to install Python is a no-no. Mercator 2.0 is divided between two main components:

* Core
* Handlers

Where `Core` is a statically linked binary (thus no external dependencies) and `Handlers` are written in ecosystem specific language, the reason for this is two-fold:

* The target language is best equipped for handling it's ecosystem as it already contains all the necessary bits to handle the packaging
* The target language is already present on the box, if I'm developing in Java I have no problem running a handler written in Java since the necessary tooling already has to be there

Another crucial difference is that the handler specification is now declarative, and not some random code in some source file, but more about that below.

### Handler Definition

Ecosystem specific handlers are configurable via special file `handlers.yml`, which allows for specifying the following criteria:

```yaml
  - name: "Python"
    description: "PyPI Package (source)"
    filepatterns:
     - "^setup\\.py$"
    binary: "python"
    handler: "handlers/python"
```

* `Filepatterns` allow for selection of file based on regular expression matching a file name
* `binary` specifies the main executable supposed to execute the handler
* `handler` is the actual handler, the directory is relative to Mercator data directory (configurable in the same YAML, defaults to `/usr/share/mercator/`)

```yaml
  - name: "Ruby-Dist"
    description: "Installed RubyGem"
    pathpatterns:
     - "^.*/specifications/.*\\.gemspec$"
    handler: "handlers/ruby"
    binary: "ruby"
```

* `pathpatterns` allow for selection of file based on regular expression matching the whole absolute path

And finally, because Java always needs a special care, there are the following configuration keys available:

```yaml
  - name: "Java"
    description: "Java JAR"
    types:
     - "application/zip"
    inarchive:
     - "META-INF/MANIFEST.MF"
    handler: "handlers/java"
    binary: "java"
    args:
     - "-jar"
```

* `types` allows to specify a MIME type the file should have
* `inarchive` specifies a file that has to be present if `types` referes to an archive (currently only ZIP is supported)
* `args` additional arguments to the `binary`

### Handler Implementation

As stated above, each handler is implemented in the language of the ecosystem, that is, Python handler is written in Python, Java handler in Java etc.
If the handler needs any additional dependencies, those are bundled in the `handlers/` directory directly (currently Java and Python has some bundled dependencies).
