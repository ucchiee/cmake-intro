# Local Variables
set(MY_VARIABLE "value")
# 変数名は大文字
# ${MY_VARIABLE} のようにしてアクセスできる
# スコープの概念がある
#   同じスコープの場合にのみアクセス可能
#   関数を抜けたり、sub directoryを抜けた場合、そこで定義された変数にはアクセスできなくなる
# `PARENT_SCOPE` を最後につけると、直上のスコープで変数を定義できる

## List
set(MY_LIST "one" "two")
set(MY_LIST "one;two")  # Same as above
# internally become ";" separated value
# `separate_arguments()`: 空白区切りの文字列をcmake-listに変換する
# ちなみに、空白が含まれない場合は`"`を省略可能（するべきではないと思うが...）
# 事故を防ぐため(特にpathを扱う場合)、変数の展開は `"${VAR}"` のように行うべき

# Cache Variables
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "Description")
# コマンドラインから同名で指定されている場合はそれを使う。**上書きしない**。
# コマンドラインから指定されていない場合はここで指定されている値を使って定義する。
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "" FORCE)
mark_as_advanced(MY_CACHE_VARIABLE)
# `FORCE`を指定すると　`cmake -L ..` で設定可能な変数を確認したときに表示されない。
# `INTERNAL`: 型 (=`STRING`のところに書く)。`STRING`型でありFORCEオプションが指定されたとみなす。
# 以下は`INTERNAL`の例
set(MY_CACHE_VARIABLE "VALUE" CACHE INTERNAL "")
# `BOOL`型は以下のように型を明示的に指定しなくても良い
set(MY_CACHE_VARIABLE "This is settable from the command line" OFF)

# Environment varialbes
# set(ENV{variable_name} value)  # set
# $ENV{variable_name}  # get
# これらの使用は避けるべき

# The Cache
# CMakeCache.txt (in the build directory) に cache variable が保存されている。
# Configure した時の値がここに保存されている

# Properties
# Properties にも情報を格納できる。
# 変数と似ているが、directoryやtargetごとに割り当てられる。
# A global property = uncached global variable
# Target property は対応する `CMAKE_` で始まる変数から初期化される
set_property(TARGET TargetName PROPERTY CXX_STANDARD 11)
set_target_properties(TargetName PROPERTIES CXX_STANDARD 11)
# set_tests_properties()
# set_target_properties()
# set_directory_properties()
# set_source_files_properties()
# set_property()のほうがより一般的な関数で、複数のtarget/file/testにpropertyを設定できる。便利なオプションもある。
# その他はショートカットと考えて良い。
get_property(ResultVariable TARGET TargetName PROPERTY CXX_STANDARD)
# このようにしてpropertyをgetできる
# 一覧はここを参照：https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html
