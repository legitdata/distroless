package(default_visibility = ["//visibility:public"])

load("@io_bazel_rules_docker//docker:docker.bzl", "docker_build")
load("@package_bundle//file:packages.bzl", "packages")
load("//cacerts:java.bzl", "cacerts_java")
load("@io_bazel_rules_docker//java:image.bzl", "java_image")

cacerts_java(
    name = "cacerts_java",
)

[docker_build(
    name = "openj9" if (not mode) else mode[1:],
    base = "//cc" + mode,
    debs = [
        packages["zlib1g"],
    #     packages["openjdk-11-jre-headless"],
    ],
    tars = [
        ":cacerts_java",
        "@openj9//:tar"
    ],
    entrypoint = [
        "/usr/bin/java",
        "-jar",
        # We expect users to use:
        # cmd = ["/path/to/deploy.jar", "--option1", ...]
    ],
    symlinks = {
        "/usr/bin/java": "/openj9/jdk-11+28-jre/bin/java",
    },
) for mode in [
    "",
    ":debug",
]]

load("@io_bazel_rules_docker//contrib:test.bzl", "container_test")

container_test(
    name = "openj9_test",
    configs = ["testdata/java.yaml"],
    image = ":1",
)

java_image(
    name = "check_certs",
    srcs = ["testdata/CheckCerts.java"],
    base = "//java:openj9",
    main_class = "testdata.CheckCerts",
)

container_test(
    name = "check_certs_test",
    configs = ["testdata/java.yaml"],
    image = ":openj9",
)
