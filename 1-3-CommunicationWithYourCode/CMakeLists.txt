# Configure File
# ソースコード中からCMake変数にアクセスするための仕組み。
# `configure_file()` を使うことによりCMake変数を置換する。
# Version.h.in (.in拡張子にするのが慣習らしい):
# ```
# #pragma once
#
# #define MY_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
# #define MY_VERSION_MINOR @PROJECT_VERSION_MINOR@
# #define MY_VERSION_PATCH @PROJECT_VERSION_PATCH@
# #define MY_VERSION_TWEAK @PROJECT_VERSION_TWEAK@
# #define MY_VERSION "@PROJECT_VERSION@"
# ```
configure_file(
  "${PROJECT_SOURCE_DIR}/include/My/Version.h.in"
  "${PROJECT_BINARY_DIR}/include/My/Version.h"
)
# このコマンドでVersion.h.inの中のCMake変数を置換してVersion.hに変換することができる。
# `.cmake`ファイルの生成に使える。（らしい）

# Reading files
# ソースコードをCMakeから読むこともできる。
# 正規表現を使ってバージョンを取り出す、みたいなユースケースっぽい。以下はその例:
# Header only library の場合重宝しそうだよね。

# Assuming the canonical version is listed in a single line
# This would be in several parts if picking up from MAJOR, MINOR, etc.
set(VERSION_REGEX "#define MY_VERSION[ \t]+\"(.+)\"")

# Read in the line containing the version
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/include/My/Version.hpp"
  VERSION_STRING REGEX ${VERSION_REGEX})

# Pick out just the version
string(REGEX REPLACE ${VERSION_REGEX} "\\1" VERSION_STRING "${VERSION_STRING}")

# Automatically getting PROJECT_VERSION_MAJOR, My_VERSION_MAJOR, etc.
project(My LANGUAGES CXX VERSION ${VERSION_STRING})
