# git-extension

### What is it?

git-command の追加や拡張

以下のコマンドを実行時すると、gitのサブコマンドが配置されたディレクトリのパスが表示される
```bash
$ git --exec-path
```

ここ、あるいはPATHが通ったディレクトリに 'git-subcommand' という名前でファイルを配置すると、git-commandを追加できる

ex: git管理下のファイル総数を調べる

1. ファイル数を数える script(shでもpythonでもなんでもいい) を作成し 'git-fc' という名前で保存する
1. PATH が通った場所に 'git-fc' を配置する
1. git管理下のパスで、 **git fc** や **git fc --all** あるいは **git fc -- src/\*.sh** などのコマンドを打つ *こういったオプションが使用できるように script を開発する必要がある*

### Contents

- git-hazard.sh
  - Git管理下のファイルの、汚染度を調べる
  - `git hazard -u puck src/app.py`
