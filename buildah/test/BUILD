gentest(
    name = "test",
    timeout = 30,
    data = {
        "push": [
            "//buildah/examples:rootless_alpine_install_push",
            "//buildah/examples:rootless_ubuntu_install_push",
            "//buildah/examples:binary_push",
        ],
        "registry": [
            ":test_registry",
        ],
    },
    no_test_output = True,
    test_cmd = """
set -euo pipefail
2>/dev/null 1>&2 $DATA_REGISTRY &
registry_pid=$!
for push in $DATA_PUSH; do
    2>/dev/null 1>&2 $DATA_PUSH
done
kill $registry_pid || true
""",
)

genrule(
    name = "registries_test",
    outs = ["registries_test.conf"],
    cmd = """
cat <<EOF > $OUTS
[registries.search]
registries = ['docker.io']
[registries.insecure]
registries = ['localhost:5000']
EOF
    """,
    visibility = ["PUBLIC"],
)

sh_cmd(
    name = "test_registry",
    cmd = """
root_dir="$(dirname $(dirname $(dirname $PWD | sed "s#$PKG_DIR##")))"
\\\\$root_dir/$(out_exe :_test_registry_download) serve \\
    \\\\$root_dir/$(out_location :_test_registry_config)
""",
    data = [
        ":_test_registry_config",
        ":_test_registry_download",
    ],
    visibility = ["PUBLIC"],
)

genrule(
    name = "_registry_test_certificates",
    outs = [
        "test.crt",
        "test.key",
    ],
    cmd = """
openssl req -newkey rsa:2048 \\
    -x509 \\
    -sha256 \\
    -nodes \\
    -out test.crt \\
    -keyout test.key \\
    -subj "/C=US/ST=test/L=test/O=test/CN=localhost" \\
    -addext "subjectAltName = DNS:localhost"
    """,
)

remote_file(
    name = "_test_registry_download",
    binary = True,
    hashes = [""],
    url = "https://raw.githubusercontent.com/docker/distribution-library-image/master/amd64/registry",
)

genrule(
    name = "_test_registry_rootdir",
    outs = ["_test_registry_rootdir"],
    cmd = "mkdir $OUTS",
)

genrule(
    name = "_test_registry_config",
    outs = ["test-registry-config.yaml"],
    cmd = """
root_dir="$(dirname $(dirname $(dirname $PWD | sed "s#$PKG_DIR##")))"
certificate_outs=($(out_locations :_registry_test_certificates))
key_out="$root_dir/$(printf "%s\n" "${certificate_outs[@]}" | grep "\\.key")"
cert_out="$root_dir/$(printf "%s\n" "${certificate_outs[@]}" | grep "\\.crt")"
cat <<EOF > $OUTS
version: 0.1
log:
  fields:
    service: registry
storage:
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: "$root_dir/$(out_location :_test_registry_rootdir)"
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
  tls:
    certificate: "$cert_out"
    key: "$key_out"
EOF
    """,
    data = [
        ":_registry_test_certificates",
        ":_test_registry_rootdir",
    ],
)
