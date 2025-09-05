build_rule(
    name = "default_entrypoint",
    outs = ["default_entrypoint.txt"],
    # Default is Bash
    cmd = "printf '%s' \"$NAME\" > $OUTS",
)

sh_test(
    name = "default_entrypoint_test",
    src = "test_build_entrypoint.sh",
    data = [":default_entrypoint"],
)

build_rule(
    name = "bash_entrypoint",
    outs = ["bash_entrypoint.txt"],
    build_entrypoint = {
        "entrypoint": ["bash", "--noprofile", "--norc", "-u", "-o", "pipefail"],
        "exit_on_error_flags": ["-e"],
        "exec_command_flags": ["-c"],
    },
    cmd = "printf '%s' \"$NAME\" > $OUTS",
)

sh_test(
    name = "bash_entrypoint_test",
    src = "test_build_entrypoint.sh",
    data = [":bash_entrypoint"],
)

subinclude("///go//build_defs:go")

go_binary(
    name = "main",
    srcs = ["main.go"],
)

build_rule(
    name = "binary_entrypoint",
    outs = ["binary_entrypoint.txt"],
    build_entrypoint = {
        "entrypoint": ["$(exe :main)"],
    },
    tools = [":main"],
)

sh_test(
    name = "binary_entrypoint_test",
    src = "test_build_entrypoint.sh",
    data = [":binary_entrypoint"],
)

nushell_version = "0.104.1"
nushell_release_asset_suffix_by_os_arch = {
    "linux_amd64": "-x86_64-unknown-linux-gnu.tar.gz",
    "linux_arm64": "-aarch64-unknown-linux-gnu.tar.gz",
    "darwin_amd64": "-x86_64-apple-darwin.tar.gz",
    "darwin_arm64": "-aarch64-apple-darwin.tar.gz",
}
nushell_release_asset_suffix = nushell_release_asset_suffix_by_os_arch[CONFIG.OS+"_"+CONFIG.ARCH]
nushell_release_asset_extracted_suffix = nushell_release_asset_suffix.split(".")[0]

remote_file(
    name = "nu",
    url = f"https://github.com/nushell/nushell/releases/download/{nushell_version}/nu-{nushell_version}-{nushell_release_asset_suffix}",
    extract = True,
    binary = True,
    exported_files = [f"nu-{nushell_version}-{nushell_release_asset_extracted_suffix}/nu"],
)

build_rule(
    name = "nushell_entrypoint",
    outs = ["nushell_entrypoint.txt"],
    build_entrypoint = {
        "entrypoint": ["$(exe :nu)", "--no-config-file", "--no-history"],
        "interactive_flags": ["--execute", "$env.config.show_banner = false"],
        "exec_command_flags": ["--commands"],
    },
    cmd = "$env.NAME | save $env.OUTS",
    tools = [":nu"],
)

sh_test(
    name = "nushell_entrypoint_test",
    src = "test_build_entrypoint.sh",
    data = [":nushell_entrypoint"],
)

powershell_version = "7.5.2"
powershell_release_asset_suffix_by_os_arch = {
    "linux_amd64": "-linux-x64.tar.gz",
    "linux_arm64": "-linux-arm64.tar.gz",
    "darwin_amd64": "-osx-x64.tar.gz",
    "darwin_arm64": "-osx-arm64.tar.gz",
}
powershell_release_asset_suffix = powershell_release_asset_suffix_by_os_arch[CONFIG.OS+"_"+CONFIG.ARCH]

remote_file(
    name = "powershell_toolchain",
    url = f"https://github.com/PowerShell/PowerShell/releases/download/v7.5.2/powershell-7.5.2-{powershell_release_asset_suffix}",
    extract = True,
    out = "powershell_toolchain",
    entry_points = {
        "pwsh": "powershell_toolchain/pwsh",
    },
    binary = True,
)

build_rule(
    name = "powershell_entrypoint",
    outs = ["powershell_entrypoint.txt"],
    build_entrypoint = {
        "entrypoint": ["$(exe :powershell_toolchain)/pwsh", "-NoProfile"],
        "interactive_flags": ["-Interactive"],
        "exec_command_flags": ["-Command"],
    },
    cmd = '$Env:NAME | Out-File -FilePath $Env:OUTS',
    tools = [":powershell_toolchain"],
)

sh_test(
    name = "powershell_entrypoint_test",
    src = "test_build_entrypoint.sh",
    data = [":powershell_entrypoint"],
)
