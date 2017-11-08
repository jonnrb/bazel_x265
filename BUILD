package(default_visibility = ["//visibility:private"])

X265_VERSION = "136"

config_setting(
    name = "linux",
    values = {"cpu": "k8"},
    visibility = ["//visibility:public"],
)

config_setting(
    name = "macos",
    values = {"cpu": "darwin"},
    visibility = ["//visibility:public"],
)

cc_library(
    name = "x265",
    hdrs = [
        "x265.h",
        "x265_config.h",
    ],
    srcs = [
        "libx265.so." + X265_VERSION,
        "libx265.pic.a",
    ],
    linkopts = select({
        ":linux": ["-lnuma"],
        ":macos": [],
    }),
    visibility = ["//visibility:public"],
)

cc_library(
    name = "x265_isystem",
    hdrs = ["include/x265.h"],
    includes = ["include"],
    visibility = ["//visibility:public"],
)

genrule(
    name = "isystem_x265_h",
    srcs = ["x265.h"],
    outs = ["include/x265.h"],
    cmd = "cp $< $@",
)

genrule(
    name = "make",
    srcs = glob(["x265/**"]),
    outs = [
        "x265.h",
        "x265_config.h",
        "libx265.so." + X265_VERSION,
        "libx265.pic.a",
    ],
    cmd = "cmake $$(dirname $(location x265/source/CMakeLists.txt))" +
          "  -DHIGH_BIT_DEPTH=1 -DMAIN12=0 -DENABLE_CLI=0 > /dev/null;" +
          "make > /dev/null;" +
          "mv libx265.so." + X265_VERSION +
          "  $(location libx265.so." + X265_VERSION + ");" +
          "mv libx265.a $(location libx265.pic.a);" +
          "mv $(location x265/source/x265.h) $(location x265.h);" +
          "mv x265_config.h $(location x265_config.h);" +
          "",
)
