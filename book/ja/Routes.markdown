Routes
======

HTTP メソッド
------------
SinatraのroutesはHTTPリクエストのメソッドに応答するように設計されています。

* GET
* POST
* PUT
* DELETE

基本
-----

シンプル

    get '/hi' do
      ...
    end
    
パラメータ付きリクエスト

    get '/:name' do
      # matches /sinatra and the like and sets params[:name]
    end

オプション
-------

Splat
------

    get '/say/*/to/*' do
      # matches /say/hello/to/world
      params["splat"] # => ["hello", "world"]
    end
    
    get '/download/*.*' do
      # matches /download/path/to/file.xml
      params["splat"] # => ["path/to/file", "xml"]
    end


ユーザエージェント
----------

    get '/foo', :agent => /Songbird (\d\.\d)[\d\/]*?/ do
      "You're using Songbird version #{params[:agent][0]}"
    end
    
    get '/foo' do
      # matches non-songbird browsers
    end

その他のメソッド
-------------

その他のメソッドは「get」のrouteと全く同じようにリクエストされます。
`get`の代わりに単純に`post`、`put`または `delete`関数をrouteの定義に使うだけです。
POSTのパラメータにアクセスするには、`params[:xxx]`を使用します。
ここでxxxはポストされてきたフォーム要素の名前です。

    post '/foo' do
      "You just asked for foo, with post param bar equal to #{params[:bar]}"
    end


PUT と DELETE メソッド
--------------------------

ブラウザがPUTとDELETEメソッドをネイティブでサポートしていないため、
Webコミュニティによってハック的な対処法が導入されています。
Sinatraでこの対処法を使うために２つの方法があります：

１つ目は、「\_method」という名前のhidden要素をフォームに追加し、
その値に使いたいHTTPメソッドの名前を設定します。
このフォーム自体はPOSTとして送信されますが、Sinatraは望んだ
メソッドとして解釈します。例えば：

    <form method="post" action="/destroy_it">
      <input type="hidden" name="_method" value="delete" />
      <div><button type="submit">Destroy it</button></div>
    </form>

そしてアプリケーションに、次のように `use Rack::MethodOverride`を追加します:

    require 'sinatra'
    
    use Rack::MethodOverride
    
    delete '/destroy_it' do
      # destroy it
    end

もしくは、次のようにSinatra::Baseのサブクラスにします：

    require 'sinatra/base'
    
    class MyApp < Sinatra::Base
      use Rack::MethodOverride
      
      delete '/destroy_it' do
        # destroy it
      end
    end

（CurlやActiveResource）などPUTやDELETEをサポートするクライアントを使う場合、
上記の`_method`は無視してそのままそれらを使ってください。
これはPUTやDELETEをサポートしていないブラウザのためのハックです。

どのようにrouteをみつけるか
------------------------
新しいrouteをアプリケーションに追加するたび、それにマッチするような
正規表現にコンパイルされます。それは、そのrouteに結びついたハンドラブロックと
共に配列に格納されます。

新しいリクエストが届くと、いずれかにマッチするまでそれぞれの正規表現が実行され、
そのrouteに結びつくハンドラ（コードブロック）が実行されます。

複数のファイルに分割する
-----------------------------
developmentモードにおいて、Sinatraはリクエストごとにrouteをクリアし、
アプリケーションを再読み込みを行うため、routeを含むファイルをrequireすることはできません。
なぜならrequireしたrouteはアプリケーションが起動した時だけロードされるためです。
（そして最初のリクエストの時でさえ再読み込みが行われます！）
その代わり、[load](http://www.ruby-doc.org/core/classes/Kernel.html#M005966 "Ruby RDoc: load")
を使用してください。

    # application.rb
    require 'rubygems'
    require 'sinatra'
    
    get '/' do
      "Hello world!"
    end
    
    load 'more_routes.rb'

そして

    # more_routes.rb
    
    get '/foo' do
      "Bar?  How unimaginative."
    end
