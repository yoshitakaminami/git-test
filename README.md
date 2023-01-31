# Gitを試しに使うページ

## このページについて
Gitを用いての業務の前段階としてこのリポジトリでGitの挙動について確認できればと思います。

## 1. clone
このリポジトリをcloneしてください。
```
git clone https://github.com/yoshitakaminami/git-test.git
```
git-test というディレクトリができれば成功です。
git-testディレクトリに移動し
```
git status
git branch
```
を試してください。
慣れるまでは(慣れても)statusコマンドとbranchコマンドを打つ癖を付けておくとミスが減ります。

## 2. branchの作成
master branchに居ることを確認した上で練習用branchを作成しましょう。(社内では生やすと言う表現が多いです。)

※ 20230131の部分は適宜日付に変えてください。
```
git branch training_20230131
```
その後練習用branchへ移動、自身の居るbranchを確認
```
git checkout training_20230131
git branch
```
以下のコマンドでbranchの作成と移動を一挙にできます。
```
git checkout -b training_20230131
```

## 3.ファイルのcommit
このファイルに対して適当な変更を加えてcommit します。
↓の行に自分の名前とか書いてください。
ついでに2.で作ったbranch名をファイル内で置き換えておくといいです。
※ 実際はこの部分がコーディング作業となります。

この辺に名前書く
____

↑のファイルの変更・保存が終わったら
index への登録確認
```
git status
```
ファイルをstageに登録(社内ではaddするとか言います。)
index.html部分はファイル名です。
半角スペースを空けて複数登録可能
ディレクトリを指定するとディレクトリ内のファイルを全て登録可能
```
git add index.html
```
ファイルがstageされたことを確認
```
git status
```
stageされたファイルをcommitします。
このあたりから作業の差し戻しが面倒になるので注意
※ メッセージはリポジトリによって記述ルールがあることあがります。
　特にルールが無くても何をしたcommitなのかを記述するようにしてください。(日本語でOKが)
```
git commit -m "commit メッセージ"
```
index, stageにファイルが無いことを確認
今居るbranchが作業branchであることを確認
```
git status
git branch
```
commit をpushする
```
git push origin training_20230131
```

※ 社内ではcommit しといてください。等の表現もありますが言語の統一ができていないことがあります。
　できればpushまでするのかを確認するようにしてください。

## 4. pull requestの作成・review・merge
repositoryのホスティングサイト(github, bitbucket)に行きpull request(プルリク・pr)を作成します。
作成方法はサイトによってことなります。

※ 業務フローに沿ってpull requestの向き先(base)branchを設定してください
　今回はmaster branchに向けるようにしてください
　reviewerを設定しreview依頼してください。
　repositoryによっては特定のreviewerの承認ナシや自動テストを通過しないとmergeできないケースがあります。
　mergeは指示があるまで待つようにしてください。
　大抵は承認後マージ依頼や上長がマージする旨の連絡来ます。
　repositoryの設定に依りますが、mergeすることでリリースフローに乗るケースがあります。

## 5. merge先branchの更新確認
ホスティングサイトでmergeまで実行できたら、ローカルで向き先branchに移動(checkout)して差分が反映されていることを確認

```
git checkout master
git branch
```
エディタでindex.htmlを開いて変更確認できればOKです。

サイト制作などでFTPでファイルをアップする際は、必ず本番環境にmaster branchのファイルがアップされている状態を保ってください。

___
一般的な業務フローはここまでです。
次からトラブルシューティング(conflict解消)と後片付け(revert)します。
___

## 6. conflict
複数人で同一ファイルを弄るとconflictが発生することがあります。
Remote-tracking branch(追跡branch)の同期
```
git fetch
```
同期の確認(-aを付けることで追跡branch含めての一覧表示)
```
git branch -a
```
追跡branchの中のconflict_check branchからconflict確認用のbranchを作成
※ ここでも20230131は適宜変更してください。
　以下のコマンドでconflict_check からconflict_check_20230131を生やし移動までできます。
```
git checkout -b conflict_check_20230131 origin/conflict_check
git branch
```
conflict_check_20230131からtraining_20230131をbase branchとしたプルリクを作ってみます。
```
git push origin conflict_check_20230131
```
プルリクを作成するとconflict発生の旨が表示されます。
ホスティングサイトに依ってはその場で解消することもできますが、できないケースも多いので大抵ローカルで解消commitを作成します。
以下のコマンドでローカルでmergeを行ない、conflictを発生させます。
```
git branch
git merge training_20230131
```
conflict発生の旨が記載されます。
またgitのindexにも該当ファイルが登録されるようになります。
```
git status
```
エディタを開きconflictした箇所のうち残したい差分を残しcommit&pushします。
今回はtraining_20230131側の修正を残します。
※ 他者のcommit とconflictした場合は協議の上どちらの差分を残すか決定してください。
※ 差分を残すという表現を使ってますが、両方残すケースやプログラムの動作状況・仕様次第では全く別の記述が求められることがあります。
```
git branch
git status
git add index.html
git commit -m "training_20230131とのconflictの解消"
git push origin conflict_check_20230131
```
ホスティングサイトでプルリクを確認するとconflictが解消さmergeできるようになっているのがわかります。
以降はreviewしてもらいmergeする流れは通常フローと一緒です。

##  7. あと片付け(revert)
一連の流れでmaster branchはtraining_20230131を取り込んだ状態となります。
間違った差分を取り込んでしまった場合、やり直すためググるとresetコマンドが出てくることがあります。
resetコマンドは運用が難しいため慣れるまで(慣れて？)使用しないようにしてください。
代替としてrevertコマンドを使用します。
revertコマンドはcommit をなかったことにするcommit を作成してくれます。
```
git status \\index等が残っていないのを確認
git branch
git checkout master
git log
```
git logで過去のcommit 履歴が表示されます。
commit IDをコピーしてrevert用のbranchを作成します。
```
git checkout -b revert_20230131
git revert [commit ID]
git push origin revert_20230131
```
プルリク作成後、承認を経てmaster branchにマージされた後、
不要なプルリク/branchの削除で片付け終了です。

