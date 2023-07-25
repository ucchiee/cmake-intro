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
