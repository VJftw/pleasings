Buildah build rules
===================

These build defs contain a set of rules for using [Buildah](https://buildah.io/) to build docker images with plz.

We use `buildah` to build Docker images in a daemonless, rootless manner so that we do not need to have `docker` installed or running.

This includes support for the following:
 * `buildah_image`: Build a docker image from a Dockerfile via `buildah`.
 * `buildah_image_binary`: Build a docker image from a binary.

## Usage

Alongside using `remote_file` to download the `buildah.build_defs`, you also need to do the following to use these Buildah build_defs.

### 1. Buildah tool (`buildah-tool`)

The `buildah` tool can be installed natively on your operating system via the methods here: https://github.com/containers/buildah/blob/master/install.md

Alternatively, if you're on `linux_amd64`, you can use a `remote_file` rule e.g.:
```python
# //third_party/binary/BUILD
remote_file(
    name = "buildah",
    binary = True,
    hashes = ["b0faff239ed4e7ced19beccd5818ae9d437663ca50f746e61325c26b4b539ffd"],
    # To obtain a release binary URL
    # 1. Look for desired release branch under https://cirrus-ci.com/github/containers/buildah
    # 2. Open latest passing build of that release branch
    # 3. Open cirrus-ci/only_prs/static_binary
    # 4. Copy the Task number from the address bar and replace the task number below
    url = f"https://api.cirrus-ci.com/v1/artifact/task/5281926619594752/binaries/result/bin/buildah",
    visibility = ["PUBLIC"],
)
```
with a `buildah-tool` entry in your `.plzconfig_linux_amd64`, e.g.:
```conf
; .plzconfig_linux_amd64
[buildconfig]
; buildah
buildah-tool = //third_party/binary:buildah
```

### 2. Skopeo Policy configuration (`buildah-policy`)
https://www.mankier.com/5/containers-policy.json

If you're coming from docker, a default policy is available here: https://raw.githubusercontent.com/containers/skopeo/master/default-policy.json
which can be used via:
```python
# //third_party/buildah/BUILD
remote_file(
    name = "policy",
    hashes = ["cddfaa8e6a7e5497b67cc0dd8e8517058d0c97de91bf46fff867528415f2d946"],
    url = "https://raw.githubusercontent.com/containers/skopeo/master/default-policy.json",
    visibility = ["PUBLIC"],
)
```
with a `buildah-policy` entry in your `.plzconfig`, e.g.:
```conf
; .plzconfig_linux_amd64
[buildconfig]
; buildah
buildah-policy = //third_party/buildah:policy
```


### 3. Buildah Registries configuration (`buildah-registries`)
https://www.mankier.com/5/containers-registries.conf

If you're coming from docker, a default registries config is available here: 
which can be used via:
```python
remote_file(
    name = "registries",
    hashes = ["9cd209f73df76c799ec85ec00c065f60f3ef55265253483ce63049bc7438f3e4"],
    url = "https://raw.githubusercontent.com/containers/buildah/master/docs/samples/registries.conf",
    visibility = ["PUBLIC"],
)
```
with a `buildah-registries` entry in your `.plzconfig`, e.g.:
```conf
; .plzconfig_linux_amd64
[buildconfig]
; buildah
buildah-registries = //third_party/buildah:registries
```

### 4. (Optional) Generated repository names

We offer the following configuration parameters for generating repository names, instead of manually entering them for each `buildah_image`/`buildah_image_binary`.

#### `buildah-repository-prefix` 
This prefixes all of the image repository names with the given value. This is useful for building images which will be pushed to a single registry, e.g. setting to `index.docker.io/my-namespace/` will tag all built images with that prefix.

#### `buildah-generated-repository-pkg-offset`
When no repository name is given, we generate a repository name from the please `$PKG:$NAME`. This configuration allows to offset the $PKG. e.g.:
* a value of `0` results in `//images:my-image becoming` `index.docker.io/my-namespace/images/my-image`.
* a value of `1` results in `//images:my-image becoming` `index.docker.io/my-namespace/my-image`.
