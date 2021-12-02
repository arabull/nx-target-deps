# nx-target-deps

Sample repository to show failures when targets don't exist when using "self" dependencies.

# structure

There are two projects:

- one
- two

Via implicit dependencies, project "two" depends on project "one".

Each project has a single `build` target. Via `nx.json`, projects depend on their dependencies' `build` targets.

Project "two" has a `generate-sources` target, but project "one" does not. Via `nx.json`, projects' `build` target depends on their _own_ `generate-sources` target.

## instructions

1. `npm i`
1. `nx run two:build`

## expected

Both projects should build successfully, and the `generate-sources` target for project "two" should run before that project builds.

## actual

The task fails because project "one" does not have a `generate-sources` target.

# summary

It's common to have so-called "optional" targets. These are targets that some projects may define and others may not. Generation of sources is a perfect example. Some projects may need to generate code before building, while others may not.

Projects can define dependencies at the project level using "dependsOn", and in that case, this type of failure makes perfect sense. But in a workspace with hundreds of projects, that becomes very onerous and error-prone. It would be much easier to use the "targetDependencies" mechanism, but as things stand today, every project must have the dependent target defined, even if it does nothing.

Nx handles missing targets wonderfully in other situations. For example, if I run a `run-many --all` command and point to a target that not every project defines, Nx doesn't care. It will only run the target against the projects that have it. This situation should be handled similarly.
