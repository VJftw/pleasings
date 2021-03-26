subinclude("//buildah")

buildah_image(
    name = "rootless_alpine_install",
    dockerfile = "alpine-apk-install-Dockerfile",
    visibility = ["PUBLIC"],
)

buildah_image(
    name = "rootless_ubuntu_install",
    dockerfile = "ubuntu-apt-install-Dockerfile",
    visibility = ["PUBLIC"],
)

go_binary(
    name = "_binary",
    srcs = ["binary.go"],
)

buildah_image_binary(
    name = "binary",
    binary = ":_binary",
    visibility = ["PUBLIC"],
)
