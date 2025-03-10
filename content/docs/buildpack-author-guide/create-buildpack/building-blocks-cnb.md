+++
title="Building blocks of a Cloud Native Buildpack"
weight=402
+++

<!-- test:suite=create-buildpack;weight=2 -->

Now we will set up the buildpack scaffolding.

Let's create the directory where your buildpack will live:

## Using the Pack CLI

The `buildpack new <id>` command will create a directory named for the buildpack ID.

Example:
<!-- test:exec -->
```bash
pack buildpack new examples/node-js \
    --api 0.10 \
    --path node-js-buildpack \
    --version 0.0.1 \
    --stacks io.buildpacks.samples.stacks.jammy
```
<!--+- "{{execute}}"+-->
This command will create `node-js-buildpack` directory which contains `buildpack.toml`, `bin/build`,  `bin/detect` files.

### Additional Parameters
- `-a, --api` Buildpack API compatibility of the generated buildpack
- `-h, --help` Help for 'new'
- `--path` the location on the filesystem to generate the artifacts
- `--stacks` Stacks (deprecated) the buildpack will work with
- `-V, --version` the version of the buildpack in buildpack.toml


### buildpack.toml

You will have `node-js-buildpack/buildpack.toml`<!--+"{{open}}"+--> in your buildpack directory to describe our buildpack.

<!-- test:file=node-js-buildpack/buildpack.toml -->
```toml
# Buildpack API version
api = "0.10"

# Buildpack ID and metadata
[buildpack]
  id = "examples/node-js"
  version = "0.0.1"

# Targets the buildpack will work with
[[targets]]
os = "linux"

# Stacks (deprecated) the buildpack will work with
[[stacks]]
  id = "io.buildpacks.samples.stacks.jammy"

```

The buildpack ID is the way you will reference the buildpack when you create buildpack groups, builders, etc.
[Targets](/docs/concepts/components/targets/) identifies the kind of build and run base images the buildpack will work with.
The stack ID (deprecated) uniquely identifies a build and run image configuration the buildpack will work with. This example can be run on Ubuntu Jammy.

### `detect` and `build`

Next, we will cover the `detect` and `build` scripts. These files are created in `bin` directory in your buildpack directory.


Now update your `node-js-buildpack/bin/detect`<!--+"{{open}}"+--> file and copy in the following contents:

<!-- test:file=node-js-buildpack/bin/detect -->
```bash
#!/usr/bin/env bash
set -eo pipefail

exit 1
```

Also update your `node-js-buildpack/bin/build`<!--+"{{open}}"+--> file and copy in the following contents:

<!-- test:file=node-js-buildpack/bin/build -->
```bash
#!/usr/bin/env bash
set -eo pipefail

echo "---> NodeJS Buildpack"
exit 1
```

These two files are executable `detect` and `build` scripts. You are now able to use this buildpack.

### Using your buildpack with `pack`

In order to test your buildpack, you will need to run the buildpack against your sample Ruby app using the `pack` CLI.

Set your default [builder][builder] by running the following:

<!-- test:exec -->
```bash
pack config default-builder cnbs/sample-builder:jammy
```
<!--+- "{{execute}}"+-->

Tell pack to trust our default builder:

<!-- test:exec -->
```bash
pack config trusted-builders add cnbs/sample-builder:jammy
```
<!--+- "{{execute}}"+-->

Then run the following `pack` command:

<!-- test:exec;exit-code=1 -->
```bash
pack build test-node-js-app --path ./node-js-sample-app --buildpack ./node-js-buildpack
```
<!--+- "{{execute}}"+-->

The `pack build` command takes in your Ruby sample app as the `--path` argument and your buildpack as the `--buildpack` argument.

After running the command, you should see that it failed to detect, as the `detect` script is currently written to simply error out.

<!-- test:assert=contains;ignore-lines=... -->
```
===> DETECTING
...
err:  examples/node-js@0.0.1 (1)
...
ERROR: No buildpack groups passed detection.
ERROR: failed to detect: buildpack(s) failed with err
```

<!--+ if false+-->
---

<a href="/docs/buildpack-author-guide/create-buildpack/detection" class="button bg-pink">Next Step</a>

[builder]: /docs/concepts/components/builder
<!--+ end +-->