# Control flow
set(variable "bool variable" OFF)
if(variable)
  # If variable is `ON`, `YES`, `TRUE`, `Y`, or non zero number
else()
  # If variable is `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, `""`,
  # or ends in `-NOTFOUND`
endif()
# 歴史的背景があって、`${variable}`と書かなくても`variable`で展開されるらしい。
# CMake 3.1 以降では、quoted-expansion (e.g. `"${variable}"`)されている場合は展開しない。
# （逆に、それ以前ではquoted-varibleも再帰的に展開されていたらしいorz）
# 特別なことがない限りはquoted-expansionで変数を使うべきであろう。
# Keyword:
# - Unary
#   - `NOT`, `TARGET`, `EXISTS` (file), `DEFINED`, etc.
# - Binary
#   - `STREQUAL`, `AND`, `OR`, `MATCHES` (regex), `VERSION_LESS`, `VERSION_LESS_EQUAL`, etc.
# - `()` can be used to group

# Generator Expressions (odd and specialized らしい...orz)
#
# ほとんどのCMakeコマンドはconfigure時に実行される。
# Build時やInstall時にロジックが必要になったらどうする??->Generator Expression
# Target Property を用いてロジックを変更するらしい。
#
# 最もシンプルなgenerator expressionはinformational expressionらしい:
#   - `$<KEYWORD>`: 現在のconfigurationから評価するのかな？
#   - `$<KEYWORD:value>`:
#     - KEYWORD->評価を制御するkeywordの場合
#     - value->どの項目を評価するか。項目の値がgenerator expressionの値となる。
#   - `$<KEYWORD:value>`:
#     - KEYWORD->generator expression もしくは 0or1に評価される変数の場合
#     - value->KEYWORDが1に評価されたらこの式を`value`と評価する。
target_compile_options(MyTarget PRIVATE "$<$<CONFIG:Debug>:--my-flag>")
# これは、DEBUG configurationの場合にのみフラグを追加する例
# `*_DEBUG`変数から行うよりもよいらしい。

################ 追記 ################
# Generator Expressions がわからなかったのでここにまとめる
# 参考資料: https://qiita.com/mrk_21/items/57075ce36f49ce0aacf4
#
# Generator Expression には 3種類ある:
# - Logical Expressions
#   - 以下のif式と条件式を扱う
# - Informational Expressions
# - Output Expressions
#
# Logical Expressions
# 条件を満たしたいときに値を出力したい、という需要を満たす。
## If式:
# - 形: `$<<cond-expr>:<value>>`
#   - `<cond-expr>`は`1`が真、`0`が偽。それ以外に評価されるとエラー。
#   - `<cond-expr>`は空白を含めるとエラー。
#   - `value`: 条件式が真の時に出力される値。偽の時の値は指定できない。
#     ```
#     $<0:1>  # OK
#     $<1:2>  # OK
#     $<2:3>  # エラー
#     $<a:4>  # エラー
#     $<0 :5> # エラー
#     ```
#
## 条件式 (cond-expr):
# - 形: `$<<expr-name>:<arg1>[,...]>` or `0`, `1`
#   - `<expr-name>`: 条件式名
#   - `arg1,[...]`: 条件の評価に使う値
# - expr-nameの種類:
#   - ブール演算
#     - BOOL: if()コマンドのブール定数をif式のブール定数に変換
#       ```
#       `$<BOOL:1>`             # 1
#       `$<BOOL:2>`             # 1
#       `$<BOOL:TRUE>`          # 1
#       `$<BOOL:Yes>`           # 1
#       `$<BOOL:on>`            # 1
#
#       `$<BOOL:>`              # 0
#       `$<BOOL:0>`             # 0
#       `$<BOOL:FALSE>`         # 0
#       `$<BOOL:No>`            # 0
#       `$<BOOL:off>`           # 0
#       `$<BOOL:Hoge-NotFound>` # 0
#       ```
#     - AND,OR,NOT: if式のブール定数を取り、and,or,notの結果を返す
#   - 比較
#     - `$<EQUAL:<left>,<right>>`
#     - `$<STREQUAL:<left>,<right>>`
#     - `$<VERSION_GREATER:<left>,<right>>`
#     - `$<VERSION_LESS:<left>,<right>>`
#     - `$<VERSION_EQUAL:<left>,<right>>`
#   - 現在のコンパイラが指定したものかどうか
#     - `$<C_COMPILER_ID:<compiler-id>>`
#     - `$<CXX_COMPILER_ID:<compiler-id>>`
#     - `$<C_COMPILER_VERSION:<compiler-version>>`
#     - `$<CXX_COMPILER_VERSION:<compiler-version>>`
#   - 現在のプラットフォームが指定したものかどうか
#     - `$<PLATFORM_ID:<platform-id>>`
#     - Darwin, Linux, Windows, など
#   - 指定したターゲットに関するポリシーが最新の動作かどうか
#
# Informational Expressions
# Target property などを取得するもの
#
# - 形: `$<<expr-name>[:<arg1>[,...]>`
#   - `<expr-name>`: 取得したい式名
#   - `<expr-name>`: 取得したい項目
# - 取得できる値の種類:
#   - コンパイラの情報
#     - `$<C_COMPILER_ID>`
#     - `$<CXX_COMPILER_ID>`
#     - `$<C_COMPILER_VERSION>`
#     - `$<CXX_COMPILER_VERSION>`
#   - ターゲットの情報
#     - パス
#       - `$<TARGET_FILE:<target-name>>`
#       - `$<TARGET_FILE_NAME:<target-name>>`
#       - `$<TARGET_FILE_DIR:<target-name>>`
#       - `$<TARGET_LINKER_FILE:<target-name>>`
#       - `$<TARGET_LINKER_FILE_NAME:<target-name>>`
#       - `$<TARGET_LINKER_FILE_DIR:<target-name>>`
#       - `$<TARGET_SONAME_FILE:<target-name>>`
#       - `$<TARGET_SONAME_FILE_NAME:<target-name>>`
#       - `$<TARGET_SONAME_FILE_DIR:<target-name>>`
#     - property
#       - `$<TARGET_PROPERTY:[<target-name>,]<property-name>>`
#         - `<target-name>`を省略すると、generator exprを設定したtargetのプロパティ
#
# Output Expressions
# 値の変換、特殊記号の出力などを行う
#
# 形: `$<<expr-name>[:<arg1>[,...]>`
#   - `<expr-name>`: 式名
#   - `arg1>[,...]`: 変換したい値
# 値の変換:
#   - `$<JOIN:<list>,<sep>`: <list>を<sep>で連結
#   - `$<LOWER_CASE:<string>>`: <string>を小文字に(UPPERも同様)
# 特殊な文字の出力:
#   - `$<ANGLE-R>`: `>`
#   - `$<COMMA>`: `,`
#   - `$<SEMICOLON>`: `;`
# ある状況の時のみ出力する
#   - `$<BUILD_INTERFACE:<content>>`: build時にのみ<content>が出力される。
#   - `$<INSTALL_INTERFACE:<content>>`: install時にのみ<content>が出力される。
# その他
#   - `$<TARGET_NAME:<target-name>>`: <target-name> をターゲットとみなす。
################ 追記おわり ##########

target_include_directories(
  MyTarget
  PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

# Macros and Functions
# Functions: スコープあり、上位スコープには`PARENT_SCOPE`でアクセス
# Macros: スコープなし

function(SIMPLE REQUIRED_ARG)
  message(STATUS "Simple arguments: ${REQUIRED_ARG}, followed by ${ARGN}")
  set(${REQUIRED_ARG} "From SIMPLE" PARENT_SCOPE)
endfunction()

simple(This Foo Bar)
message("Output: ${This}")
# output:
# ```
# -- Simple arguments: This, followed by Foo;Bar
# Output: From SIMPLE
# ```
# `ARGN`: 指定していない引数は全てここに入る
# `ARGV`: 全ての引数がここに格納されている

# Arguments
# 関数引数のパースは少し面倒。`cmake_parse_arguments()`を使おう。
function(COMPLEX)
  cmake_parse_arguments(
    COMPLEX_PREFIX
    "SINGLE;ANOTHER" # <option>
    "ONE_VALUE;ALSO_ONE_VALUE" # <one_value_keywords>
    "MULTI_VALUES" # <multi_value_keywords>
    ${ARGN}
  )
endfunction()

complex(SINGLE ONE_VALUE value MULTI_VALUES some other values)

# `cmake_parse_arguments()`呼び出し直後で以下の変数が設定される:
# ```
# COMPLEX_PREFIX_SINGLE = TRUE
# COMPLEX_PREFIX_ANOTHER = FALSE
# COMPLEX_PREFIX_ONE_VALUE = "value"
# COMPLEX_PREFIX_ALSO_ONE_VALUE = <UNDEFINED>
# COMPLEX_PREFIX_MULTI_VALUES = "some;other;values"
# ```
