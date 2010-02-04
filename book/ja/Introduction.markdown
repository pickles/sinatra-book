はじめに
=============

Sinatraとはなにか？
----------------
SinatraはRubyでWebアプリケーションを素早く作るためのドメイン固有言語（DSL)です。

開発者とそのアプリケーションに最適なツールだけ最小限の機能を持っています。

Sintraはあなたのアプリケーションが次のようなものであることしか仮定しません：

* Rubyで記述されている
* URLsを持っている

Sinatraでは、短い _アドホック_ なアプリケーションでも、しっかりした
大きなアプリケーションでも同じ容易さで書くことができます。
（「実際のアプリケーション」の章を参照してください。）

Rubyの様々なRubygemsやその他のライブラリの力を使うことができます。

Sinatraは実験やアプリケーションのモックアップや素早くインターフェースを
作るようなときに本領を発揮します。

これは _典型的_ なモデル－ビュー－コントローラフレームワークではありません。
特定のURLを関連するRubyのコードに直接結びつけ、その結果をの出力をレスポンスに返します。
しかし、例えば _ビュー_ をアプリケーションのコードから分離したり、
きれいで適切に構成されたアプリケーションを書くことができます。


インストール
------------
Sinatraを入手する最も簡単な方法はRubygemです。

    $ sudo gem install sinatra

### 依存関係

Sinatraは _Rack_ gem (<http://rack.rubyforge.org>)に依存しています。

また、より最適な経験をするために、ビューの作業を簡単にする 
_Haml_ (<http://haml.hamptoncatlin.com>)と
_Builder_ (<http://builder.rubyforge.org>)のgemをインストールすると良いでしょう。

    $ sudo gem install builder haml

### エッジバージョンを使う

Sinatraの _エッジ_ バージョンはGitのリポジトリ
**<http://github.com/sinatra/sinatra/tree/master>** にあります。

_エッジ_ バージョンで新しい機能を試したり、フレームワークに貢献したりすることができます。
Git(<http://www.git-scm.com>)がインストールされている必要があります。
そして、次の手順に従ってください：

1. cd where/you/keep/your/projects
2. git clone git://github.com/sinatra/sinatra.git
3. cd sinatra
4. cd your\_project
5. ln -s ../sinatra

次にアプリケーションに次の内容を追加してください：

    $:.unshift File.dirname(__FILE__) + '/sinatra/lib'
    require 'sinatra'

次のルートを追加することで、動作しているバージョンを確認することができます。

    get '/about' do
      "I'm running on Version " + Sinatra::VERSION
    end

そして、ブラウザで `http://localhost:4567/about` にアクセスしてください。


Hello World アプリケーション
-----------------------
Sinatraをインストールして、ケーキを食べ終わったら、最初のアプリケーションを
作ってみるのはどうでしょう？

    # hello_world.rb
    require 'rubygems'
    require 'sinatra'
    
    get '/' do
      "Hello world, it's #{Time.now} at the server!"
    end

`$ ruby hello_world.rb`でこのアプリケーションを実行し、
`http://localhost:4567` にアクセスしてください。

ご覧の通り、Sinatraはたくさんのインフラストラクチャのセットアップを強要しません。
あるURL（この例では_root_ URL）へのリクエストは、あるRubyコードを評価し、
文字列をレスポンスに返します。

Sinatraを利用している実際のアプリケ－ション
----------------------------------

### Github Services

GitのホスティングプロバイダであるGithubは、誰かがリポジトリにプッシュするごと、
ユーザの指定されたサービスまたはURLを呼び出すpost-receiveフックにSinatraを利用しています：

* <http://github.com/blog/53-github-services-ipo>
* <http://github.com/guides/post-receive-hooks>
* <http://github.com/pjhyett/github-services>

### Git Wiki

Git WikiはSinatraとGitによって作られた最小限のWikiエンジンです。
機能が追加された様々なforkもあります。

* <http://github.com/sr/git-wiki>
* <http://github.com/sr/git-wiki/network>

### Integrity

Integrity はSinatraを使った、小さくてきれいな _継続インテグレーション_サービスです。
あなたのコードベースのビルドが失敗したことを監視し、様々なチャンネルであなたに通知します。

* <http://www.integrityapp.com/>
* <http://github.com/foca/integrity>

### Seinfeld Calendar

Seinfeld Calendar は、あなたのオープンソースへの貢献を追跡する楽しいアプリケーションです。
例えば継続的にGithubのリポジトリにコミットしなど、あなたの「streaks」を表示します。

* <http://www.calendaraboutnothing.com>
* <http://github.com/entp/seinfeld>


この本について
---------------
この本はRubyの基礎的な知識と、Rubyインタプリタの使い方を知っている人を対象とします。

Rubyについてより多くの情報を得るには次のリンクをたどってください：

* <http://www.ruby-lang.org>
* <http://www.ruby-lang.org/en/documentation/ruby-from-other-languages/>
* <http://www.ruby-doc.org>
* <http://www.ruby-doc.org/core-1.8.7/index.html>
* <http://www.ruby-doc.org/docs/ProgrammingRuby/>
