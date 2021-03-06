## mbt

mbt - The most flexible build orchestration tool for monorepo

### Synopsis


mbt is a build orchestration utility for monorepo.

In mbt, we use the term `module` to identify an independently buildable unit of code.
A collection of modules stored in a repository is called a manifest.

You can turn any directory into a module by placing a spec file called `.mbt.yml`.
Spec file is written in `yaml` following the schema specified below.

```
name: Unique module name (required)
build: Dictionary of build commands specific to a platform (optional)
  os: os name (linux|darwin|windows)
    cmd: Command name (required)
    args: Array of arguments (optional)
dependencies: An array of modules that this module's build depend on (optional)
fileDependencies: An array of file names that this module's build depend on (optional)
commands: Optional dictionary of custom commands (optional)
  name: Custom command name (required)
  cmd: Command name (required)
  args: Array of arguments (optional)
	os: Array of os identifiers where this command should run (optional)
properties: Custom dictionary to hold any module specific information (optional)
```

### Build Command

Build command is operating system specific. When executing `mbt build xxx`
commands, it skips the modules that do not specify a build command for the operating 
system build is running on.
Full list of operating systems names that can be used can be 
found [here](https://golang.org/doc/install/source#environment)

### Dependencies

`mbt` comes with a set of primitives to manage build dependencies. Current build
tools do a good job in managing dependencies between source files/projects.
GNU `make` for example does a great job in deciding what parts require building.
`mbt` dependencies work one level above that type of tooling to manage build
relationship between two independent modules.

### Module Dependencies

One advantage of a single repo is that you can share code between multiple modules
more effectively. Building this type of dependencies requires some thought. mbt provides
an easy way to define dependencies between modules and automatically builds the impacted modules
in topological order.

Dependencies are defined in `.mbt.yml` file under 'dependencies' property.
It accepts an array of module names.
For example, `module-a` could define a dependency on `module-b`,
so that any time `module-b` is changed, build command for `module-a` is also executed.

An example of where this could be useful is, shared libraries. Shared library
could be developed independently of its consumers. However, all consumers
are automatically built whenever the shared library is modified.

### File Dependencies

File dependencies are useful in situations where a module should be built
when a file(s) stored outside the module directory is modified. For instance,
a build script that is used to build multiple modules could define a file
dependency in order to trigger the build whenever there's a change in build.

File dependencies should specify the path of the file relative to the root
of the repository.

### Module Version

For each module stored within a repository, `mbt` generates a unique
stable version string. It is calculated based on three source attributes in
module.

- Content stored within the module directory
- Versions of dependent modules
- Content of file dependencies

As a result, version of a module is changed only when any of those attributes
are changed making it a safe attribute to use for tagging the 
build artifacts (i.e. tar balls, container images).

### Document Generation

`mbt` has a powerful feature that exposes the module state inferred from
the repository to a template engine. This could be quite useful for generating
documents out of that information. For example, you may have several modules
packaged as docker containers. By using this feature, you could generate a
Kubernetes deployment specification for all of them as of a particular commit
sha.

See `apply` command for more details.



### Options

```
      --debug       Enable debug output
  -h, --help        help for mbt
      --in string   Path to repo
```

### SEE ALSO
* [mbt apply](mbt_apply.md)	 - Apply repository manifest over a go template
* [mbt build](mbt_build.md)	 - Run build command
* [mbt describe](mbt_describe.md)	 - Describe repository manifest
* [mbt run-in](mbt_run-in.md)	 - Run user defined command
* [mbt version](mbt_version.md)	 - Show mbt version

###### Auto generated by spf13/cobra on 16-Jun-2018
