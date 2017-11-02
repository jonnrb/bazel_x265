package(default_visibility = ["//visibility:private"])

load("@jonnrb_bazel_asm//rules:yasm_library.bzl", "yasm_library")

genrule(
    name = "copy_config",
    srcs = ["x265_config.h"],
    outs = ["x265/source/x265_config.h"],
    cmd = "cp $< $@",
)

DEFINES = [
    "-DX86_64=1",
    "-DARCH_X86_64=1",
    "-DHIGH_BIT_DEPTH=1",
    "-DBIT_DEPTH=10",
    "-DEXPORT_C_API=1",
    "-DX265_DEPTH=10",
    "-DHAVE_STRTOK_R=1",
    "-DPIC=1",
    "-DX265_NS=x265",
    "-DPREFIX",
    "-DHAVE_ALIGNED_STACK=1",
] + select({
    "@bazel_tools//src:darwin": ["-DMACOS=1"],
})

CC_COPTS = DEFINES

YASM_COPTS = select({
    "@bazel_tools//src:darwin": [
        "-f",
        "macho64",
        "-m",
        "amd64",
    ],
}) + DEFINES

cc_library(
    name = "x265",
    hdrs = ["x265/source/x265.h"],
    srcs = [":asm"] + glob([
        "x265/source/common/*.cpp",
        "x265/source/common/x86/*.cpp",
        "x265/source/encoder/*.cpp",
        #"x265/source/input/*.cpp",
        #"x265/source/output/*.cpp",
        "x265/source/*.cpp",
    ]),
    copts = CC_COPTS,
    visibility = ["//visibility:public"],
    strip_include_prefix = "x265/source",
    deps = [
        ":headers",
        ":common_headers",
        ":common_x86_headers",
        ":encoder_headers",
        ":input_headers",
        ":output_headers",
    ],
)

cc_library(
    name = "headers",
    hdrs = glob([
        "x265/source/common/*.h",
        "x265/source/common/x86/*.h",
        "x265/source/encoder/*.h",
        "x265/source/input/*.h",
        "x265/source/output/*.h",
        "x265/source/*.h",
    ]) + [
        "x265/source/x265_config.h",
    ],
    strip_include_prefix = "x265/source",
)

cc_library(
    name = "common_headers",
    hdrs = glob(["x265/source/common/*.h"]),
    strip_include_prefix = "x265/source/common",
)

cc_library(
    name = "common_x86_headers",
    hdrs = glob(["x265/source/common/x86/*.h"]),
    strip_include_prefix = "x265/source/common/x86",
)

cc_library(
    name = "encoder_headers",
    hdrs = glob(["x265/source/encoder/*.h"]),
    strip_include_prefix = "x265/source/encoder",
)

cc_library(
    name = "input_headers",
    hdrs = glob(["x265/source/input/*.h"]),
    strip_include_prefix = "x265/source/input",
)

cc_library(
    name = "output_headers",
    hdrs = glob(["x265/source/output/*.h"]),
    strip_include_prefix = "x265/source/output",
)

yasm_library(
    name = "asm",
    srcs = glob(
        [
            "x265/source/common/x86/*.asm",
        ],
        exclude = [
            "x265/source/common/x86/cpu-a.asm",
            "x265/source/common/x86/intrapred8.asm",
            "x265/source/common/x86/intrapred8_allangs.asm",
            "x265/source/common/x86/ipfilter8.asm",
            "x265/source/common/x86/pixel-32.asm",
            "x265/source/common/x86/sad-a.asm",
        ],
    ),
    copts = YASM_COPTS,
)
