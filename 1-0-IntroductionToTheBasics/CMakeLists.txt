cmake_minimum_required(VERSION 3.7...3.27)

if (${CMAKE_VERSION} VERSION_LESS 3.12)
  cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif()

# Settings of a project
project(MyProject VERSION 1.0
  DESCRIPTION "Very nice project"
  LANGUAGES CXX
)

# Making an executable
add_executable(one two.cpp three.h)
# one: name of binary, and name of target
# Any number of source files can be added

# Making a library
add_library(one STATIC two.cpp three.h)
# Type of library:
# - STATIC
# - SHARED
# - MODULE
# - INTERFACE : header-only library はこれで追加する
# - ALIAS : Will give you a new name for a target. Ref `::`.
# 指定しない場合、BUILD_SHARED_LIBSを見て`STATIC`か`SHARED`を選ぶ

# Adding a include directory
target_include_directories(one PUBLIC include)
# Adds an include directory to a target.
# Type of directory:
# - PUBLIC: executable の場合ほぼ意味なし。libraryの場合、現在のtarget(e.g. one)をlinkする他のtargetもこのincludeを見る必要があることを伝える (for 補完)
# - PRIVATE: 現在のtargetのみに影響 (これに依存しているtargetには影響を与えない)
# - INTERFACE: 現在のtargetには影響を与えず、これに依存しているtargetに影響

## Chain of targets
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
# Introduce new dependency (another <- one, another depends on one) if target is given.
# one というtargetが存在しない場合は、one というlibraryをPATHから探して追加する。
#   もしくは、libraryへのfull-pathをここに書くことも可能
#   もしくは、linker flagを渡すことも可能
# PUBLIC と言う指定は省略可能だが、省略したものと省略していないものの共存はできない
# TIPS: target名を使う。キーワード(e.g. PUBLIC)は省略しない。

# !! target を使うことで、使用するlibraryの列挙を(コンパイラオプションなどを含めた形で)行うことができる !!
# !! OpenMP のような 真のlibraryではなくともtargetを用いて表せる !!
