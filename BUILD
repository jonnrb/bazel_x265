package(default_visibility = ["//visibility:private"])

load("@jonnrb_bazel_asm//rules:yasm_library.bzl", "yasm_library")

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

genrule(
    name = "copy_config",
    srcs = ["x265_config.h"],
    outs = ["x265/source/x265_config.h"],
    cmd = "cp $< $@",
)

DEFINES = [
    "-DARCH_X86_64=1",
    "-DBIT_DEPTH=10",
    "-DEXPORT_C_API=1",
    "-DHAVE_STRTOK_R=1",
    "-DHAVE_ALIGNED_STACK=1",
    "-DHIGH_BIT_DEPTH=1",
    "-DX265_ARCH_X86=1",
    "-DX265_DEPTH=10",
    "-DX265_NS=x265",
    "-DX265_VERSION=136",
    "-DX86_64=1",
] + select({
    ":macos": [
        "-DMACOS=1",
        "-DPREFIX",
    ],
    ":linux": [],
})

CC_COPTS = DEFINES + [
    "-D__STDC_LIMIT_MACROS=1",
    "-DENABLE_ASSEMBLY=1",
    "-mavx2",
]

YASM_COPTS = DEFINES + ["-DPIC"]

cc_library(
    name = "x265",
    srcs = [":asm"] + glob([
        "x265/source/common/*.h",
        "x265/source/*.h",
        "x265/source/common/*.cpp",
        "x265/source/common/vec/*.cpp",
        "x265/source/common/x86/*.h",
        "x265/source/common/x86/*.cpp",
        "x265/source/encoder/*.h",
        "x265/source/encoder/*.cpp",
        "x265/source/input/*.h",
        #"x265/source/input/*.cpp",
        "x265/source/output/*.h",
        #"x265/source/output/*.cpp",
    ]) + [
        "x265/source/x265-extras.cpp",
    ],
    hdrs = [
        "x265/source/x265.h",
        "x265/source/x265_config.h",
    ],
    copts = CC_COPTS + [
        "-Ix265/source",
        "-Ix265/source/common",
        "-Ix265/source/encoder",
        # XXX: there really should be a more deterministic way to find the
        # location of a directory and not just $(location //foo:file)
        "-Iexternal/jonnrb_bazel_x265/x265/source",
        "-Iexternal/jonnrb_bazel_x265/x265/source/common",
        "-Iexternal/jonnrb_bazel_x265/x265/source/encoder",
    ],
    linkstatic = 1,
    strip_include_prefix = "x265/source",
    visibility = ["//visibility:public"],
)

cc_library(
    name = "x265_isystem",
    includes = ["x265/source"],
    visibility = ["//visibility:public"],
    deps = [":x265"],
)

yasm_library(
    name = "asm",
    srcs = glob(
        include = [
            "x265/source/common/x86/*.asm",
        ],
        exclude = [
            "x265/source/common/x86/intrapred8.asm",
            "x265/source/common/x86/intrapred8_allangs.asm",
            "x265/source/common/x86/ipfilter8.asm",
            "x265/source/common/x86/pixel-32.asm",
            "x265/source/common/x86/sad-a.asm",
        ],
    ),
    copts = YASM_COPTS,
)
