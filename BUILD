load("@io_bazel_rules_go//go:def.bzl", "go_path", "nogo")
load("@bazel_gazelle//:def.bzl", "gazelle")

package(licenses = ["notice"])

# The sandbox filegroup is used for sandbox-internal dependencies.
package_group(
    name = "sandbox",
    packages = [
        "//...",
    ],
)

# gopath defines a directory that is structured in a way that is compatible
# with standard Go tools. Things like godoc, editors and refactor tools should
# work as expected.
#
# The files in this tree are symlinks to the true sources.
go_path(
    name = "gopath",
    mode = "link",
    deps = [
        "//runsc",

        # Packages that are not dependencies of //runsc.
        "//pkg/sentry/kernel/memevent",
        "//pkg/tcpip/adapters/gonet",
        "//pkg/tcpip/link/channel",
        "//pkg/tcpip/link/muxed",
        "//pkg/tcpip/link/sharedmem",
        "//pkg/tcpip/link/sharedmem/pipe",
        "//pkg/tcpip/link/sharedmem/queue",
        "//pkg/tcpip/link/tun",
        "//pkg/tcpip/link/waitable",
        "//pkg/tcpip/sample/tun_tcp_connect",
        "//pkg/tcpip/sample/tun_tcp_echo",
        "//pkg/tcpip/transport/tcpconntrack",
    ],
)

# gazelle is a set of build tools.
#
# To update the WORKSPACE from go.mod, use:
#   bazel run //:gazelle -- update-repos -from_file=go.mod
gazelle(name = "gazelle")

# nogo applies checks to all Go source in this repository, enforcing code
# guidelines and restrictions. Note that the tool libraries themselves should
# live in the tools subdirectory (unless they are standard).
nogo(
    name = "nogo",
    config = "//tools:nogo.js",
    visibility = ["//visibility:public"],
    deps = [
        "//tools/checkunsafe",
        "@org_golang_x_tools//go/analysis/passes/asmdecl:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/assign:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/atomic:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/atomicalign:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/bools:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/buildtag:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/cgocall:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/copylock:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/deepequalerrors:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/loopclosure:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/lostcancel:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/nilfunc:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/nilness:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/printf:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/shift:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/stdmethods:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/structtag:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/tests:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/unmarshal:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/unsafeptr:go_tool_library",
        "@org_golang_x_tools//go/analysis/passes/unusedresult:go_tool_library",
    ],
)

# We need to define a bazel platform and toolchain to specify dockerPrivileged
# and dockerRunAsRoot options, they are required to run tests on the RBE
# cluster in Kokoro.
alias(
    name = "rbe_ubuntu1604",
    actual = ":rbe_ubuntu1604_r346485",
)

platform(
    name = "rbe_ubuntu1604_r346485",
    constraint_values = [
        "@bazel_tools//platforms:x86_64",
        "@bazel_tools//platforms:linux",
        "@bazel_tools//tools/cpp:clang",
        "@bazel_toolchains//constraints:xenial",
        "@bazel_toolchains//constraints/sanitizers:support_msan",
    ],
    remote_execution_properties = """
        properties: {
          name: "container-image"
          value:"docker://gcr.io/cloud-marketplace/google/rbe-ubuntu16-04@sha256:93f7e127196b9b653d39830c50f8b05d49ef6fd8739a9b5b8ab16e1df5399e50"
        }
        properties: {
          name: "dockerAddCapabilities"
          value: "SYS_ADMIN"
        }
        properties: {
          name: "dockerPrivileged"
          value: "true"
        }
    """,
)

toolchain(
    name = "cc-toolchain-clang-x86_64-default",
    exec_compatible_with = [
    ],
    target_compatible_with = [
    ],
    toolchain = "@bazel_toolchains//configs/ubuntu16_04_clang/10.0.0/bazel_2.0.0/cc:cc-compiler-k8",
    toolchain_type = "@bazel_tools//tools/cpp:toolchain_type",
)
