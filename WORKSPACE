workspace(name="py_test")

load("@bazel_tools//tools/build_defs/repo:git.bzl", "git_repository")
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "python_interpreter",
    urls = ["https://www.python.org/ftp/python/3.8.3/Python-3.8.3.tar.xz"],
    sha256 = "dfab5ec723c218082fe3d5d7ae17ecbdebffa9a1aea4d64aa3a2ecdd2e795864",
    strip_prefix = "Python-3.8.3",
    patch_cmds = [
        "mkdir $(pwd)/bazel_install",
        "./configure --prefix=$(pwd)/bazel_install",
        "make",
        "make install",
        # On macOS case-insensitive filesystems and windows, the binary is
        # called python.exe instead of python.
        "mv python.exe python || true",
    ],
    build_file_content = """
exports_files(["python"])

filegroup(
    name = "files",
    srcs = glob(["**"], exclude = ["**/* *", "python"]),
    visibility = ["//visibility:public"],
)
""",
)

register_toolchains("//:my_py_toolchain")

# TODO: change to upstream repo
git_repository(
    name = "rules_python",
    remote = "git@github.com:kku1993/rules_python.git",
    commit = "262fbbe5e53e75776cb8ac0df7c99bbc2c1cf668",
)

load("@rules_python//python:repositories.bzl", "py_repositories")

py_repositories()

load("@rules_python//python:pip.bzl", "pip_repositories")

pip_repositories()

load("@rules_python//python:pip.bzl", "pip_import")

pip_import(
    name = "py_deps",
    requirements = "//:requirements.txt",
    python_interpreter_target = "@python_interpreter//:python",
)

load("@py_deps//:requirements.bzl", "pip_install")

pip_install()
