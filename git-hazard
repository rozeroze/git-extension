#!/bin/bash

hazard_usage() {
   echo "Usage:"
   echo "   git hazard [-u user] [-d] [-r] filepath"
   echo ""
   echo "Description:"
   echo "   git管理下に置かれたファイルの災害レベルを測定する"
   echo ""
   echo "Options:"
   echo "   -u    災害を意味するユーザ"
   echo "   -d    詳細(途中経過)を表示する"
   echo "   -r    ハザードランクの結果を読みやすい形式で表示する"
}

# gitが存在するか
which git > /dev/null 2>&1
if [ $? -ne 0 ]
then
   echo "gitが存在しません"
   exit 1
fi

# カレントディレクトリがgit管理内にいるか
inside=$(git rev-parse --is-inside-work-tree)
if [ "$inside" != "true" ]
then
   echo "git管理下にいません"
   exit 1
fi

# option引数の変数化
while getopts u:drh OPT
do
   case $OPT in
      u) user="$OPTARG" ;;
      d) detail="show detail" ;;
      r) readable="human readable" ;;
      h) hazard_usage
         exit 0
         ;;
   esac
done

# ユーザの指定がなければ invert した git user.name を使用する
if [ "$user" = "" ]
then
   user="!$(git config --get user.name)"
fi

# 指定されたファイルのチェック
file=${@:$#}
if [ ! -f $file ]
then
   echo "$file はファイルではありません"
   exit 1
fi

[ "$detail" != "" ] && echo "user is $user"
[ "$detail" != "" ] && echo "file is $file"

# ユーザ指定をもとにフィルターコマンドを作成
# WITH: ヒストリ展開を無効
set +H
filter="wc --line"
if [[ ${user:0:1} = "!" ]]
then
   filter="grep -vc ${user:1}"
else
   filter="grep -c -E ${user//,/|}"
fi
[ "$detail" != "" ] && echo "filter is \"$filter\""
set -H

# tmpファイルを作成
tmp=$(mktemp)
# shellの正常終了時にtmpファイルを削除
trap "rm -f $tmp" EXIT
# shellの終了時にtmpファイルを削除
#trap "rm -f $tmp" EXIT INT TERM HUP

# git blame の結果をtmpに保存
git --no-pager blame $file > $tmp

# 全体の行数と、フィルタした結果の行数を取得
all=$(cat $tmp | wc --line)
[ "$detail" != "" ] && echo "cat $tmp | wc --line"
[ "$detail" != "" ] && echo "> $all"
match=$(cat $tmp | $filter)
[ "$detail" != "" ] && echo "cat $tmp | $filter"
[ "$detail" != "" ] && echo "> $match"

# 取得した行数からハザードランクを求める
rate=$(( $match * 90 / $all ))
rank=$(( $rate / 10 ))
if [ $match -ne 0 ]
then
   rank=$(( $rank + 1 ))
fi
[ "$detail" != "" ] && echo "rank is $rank"

# ハザードランク Lv.3 以上は、人間の読むものではない
if [ "$readable" = "" ]
then
   echo "hazard-rank [0-10]: $rank"
else
   if [ $rank = 0 ]
   then
      echo "$file は安全です。十分な量の水と食料があり、 $user に汚染されていません"
   elif [ $rank -le 2 ]
   then
      echo "メーデー！メーデー！現在 $user コードと交戦中。援軍を要請する"
   else
      echo "このコードは汚染されている"
   fi
fi

