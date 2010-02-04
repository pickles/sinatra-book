Rack Middleware
===============
Sinatraは、RubyのWebフレームワークのための最小限の標準的な
インターフェイスである[Rack][rack]の上に乗ります。
アプリケーション開発者のためのRackの一番面白い機能の一つは、
"ミドルウェア"のサポートです。ミドルウェアはサーバと
アプリケーションの間に位置するコンポーネントでHTTPリクエスト/レスポンスを
監視したり生成し様々な共通機能を提供します。

Sinatraはのトップレベルの{0}use{/0}メソッドを介して、Rackミドルウェアのパイプラインの構築を簡単にします：

    require 'sinatra'
    require 'my_custom_middleware'
    
    use Rack::Lint
    use MyCustomMiddleware
    
    get '/hello' do
      'Hello World'
    end

"use"の動作は（よくrackupファイルで使われる）[Rack::Builder][rack_builder]DSLのものと同一です。
たとえば、useメソッドは複数/変数をブロックの引数として受け取ります：

    use Rack::Auth::Basic do |username, password|
      username == 'admin' && password == 'secret'
    end

Rackは、ロギング、デバッグ、URLルーティング、認証、セッションハンドリングのための
様々な標準的ミドルウェアとともに配布されます。
Sinatraはこれらのたくさんのコンポーネントを設定に基づいて自動的に使います。
そのため、通常は明示的にこれらをつかう必要はありません。

[rack]: http://rack.rubyforge.org/
[rack_builder]: http://rack.rubyforge.org/doc/classes/Rack/Builder.html

