# HowToStructureYourProject

> The following information is biased. But in a good way, I think.

プロジェクトの構造は概ね以下の通りにすべし:

```
- project
  - .gitignore
  - README.md
  - LICENSE.md
  - CMakeLists.txt
  - cmake
    - FindSomeLib.cmake
    - something_else.cmake
  - include
    - project
      - lib.hpp
  - src
    - CMakeLists.txt
    - lib.cpp
  - apps
    - CMakeLists.txt
    - app.cpp
  - tests
    - CMakeLists.txt
    - testlib.cpp
  - docs
    - CMakeLists.txt
  - extern
    - googletest
  - scripts
    - helper.py
```

- `CMakeLists.txt` はsource directoryに分散されている。
  - include directoryには入っていない。
  - include directory は `/usr/include` とかにコピーできるでしょ。
    - コンフリクトがあっても大変だし。（どんな状況？）
- `CMakeLists.txt`が入っているディレクトリを使うときは`add_subdirectory()`

Helper moduleは`cmake`ディレクトリに入れる:

- `Find*.cmake`のようなmoduleはありがちなやつっぽい。
  - [こんなもの](https://github.com/CLIUtils/cmake)も
- 以下のようにして追加する:
  ```cmake
  set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})
  ```

Submoduleは`extern`ディレクトリに入れる。

`.gitignore`に`/build*`を入れておこう:

- `/build`ディレクトリを使ってビルドできるようにしておこう。
