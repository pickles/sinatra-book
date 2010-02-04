Sinatra Book
============

Sinatra Webフレームワークの「本」の形式をとったドキュメントです。

概要
-----

これはSinatraを始めるための、たくさんのチュートリアルをクックブック形式のレシピとなるでしょう。

何か助けが必要な場合は IRC (#sinatra at irc.freenode.org) に参加してください。

ファイルのレイアウト:

* _book_   - この本の文書。marukuのmarkdownフォーマットです。
* _images_ - 画像、 図表、 楽しい絵
* _source_ - この本に含まれる例のソース

[http://github.com/sinatra/sinatra](http://github.com/sinatra/sinatra)でSinatraについて学ぶことができます。


この本のビルド方法
---------------------

この本をビルドするには次のgemが必要です：

    sudo gem install thor maruku

ルートディレクトリで次のThorタスクを実行してください：

    thor book:build

貢献の仕方
-----------------

このリポジトリをForkして、[スタイルガイド](http://github.com/cschneid/sinatra-book/wikis/how-to-contribute) を必ず読んでください。そして、pullリクエストを送信して下さい。
