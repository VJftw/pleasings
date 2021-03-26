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

remote_file(
    name = "policy",
    hashes = ["cddfaa8e6a7e5497b67cc0dd8e8517058d0c97de91bf46fff867528415f2d946"],
    url = "https://raw.githubusercontent.com/containers/skopeo/master/default-policy.json",
    visibility = ["PUBLIC"],
)

remote_file(
    name = "registries",
    hashes = ["9cd209f73df76c799ec85ec00c065f60f3ef55265253483ce63049bc7438f3e4"],
    url = "https://raw.githubusercontent.com/containers/buildah/master/docs/samples/registries.conf",
    visibility = ["PUBLIC"],
)
