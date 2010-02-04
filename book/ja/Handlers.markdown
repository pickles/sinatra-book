ハンドラ
========

構造
---------

ハンドラはSinatraが「コントローラ」として使う一般的な用語です。
ハンドラはあなたのアプリケーションへ送信される
新しいHTTPリクエストの最初のエントリーポイントです。

routeの詳細については、[Routeの章](#routes)を参照してください。

フォームパラメータ
---------------
ハンドラでは送信されたフォームのパラメータにparamsハッシュから
直接アクセスすることができます。

    get '/' do
      params['post']
    end
    
### ネストしたフォームパラメータ

Sinatraバージョン0.9.0からRailsのようなネストしたパラメータのサポートが組み込まれました。
これより前のバージョンでは、[before フィルタとしてこの機能を実装する](#nested_params_as_filter)
必要があります！

    <form>
      <input ... name="post[title]" />
      <input ... name="post[body]" />
      <input ... name="post[author]" />
    </form>
    

この例のパラメータはハッシュになります：
    
    {"post"=>{ "title"=>"", "body"=>"", "author"=>"" }}

よって、普通のハッシュのようにネストしたパラメータをハンドラで使うことができます：

    params['post']['title']

リダイレクト
--------

redirectヘルパーは一般的なHTTPレスポンスコード(302)のショートカットです。

基本的な使い方は簡単です：

    redirect '/'

    redirect '/posts/1'

    redirect 'http://www.google.com'

リダイレクトはLocationヘッダをブラウザに返し、ブラウザはリクエストに従い、
指示された場所に遷移します。ブラウザがリクエストに従う事により
あなたのアプリケーション内でも、完全に別なサイトでも好きなページへ
リダイレクトすることができます。

リダイレクトのフローは次通りです：
ブラウザ → サーバ ('/'へリダイレクト) → ブラウザ ('/'をリクエスト) → サーバ ('/'を返す)

Sinatraに異なるレスポンスコードを送るようにするのはとても単純です：

    redirect '/', 303 # forces the 303 return code

    redirect '/', 307 # forces the 307 return code

セッション
--------

### デフォルトのCookieベースのセッション

SintraはCookieベースのセッションを基本的にサポートしています。
この機能を有効にするには、configureブロックまたはアプリケーションの先頭で
このオプションをenableするだけです。

    enable :sessions

    get '/' do
      session["counter"] ||= 0
      session["counter"] += 1

      "You've hit this page #{session["counter"]} time(s)"
    end

このセッションのアプローチの不都合な点は、すべてのデータがCookieに保存されることです。
Cookieには4キロバイトまでという厳しい制約を持っているため、
多くのデータを保存することができません。
他の問題としてはCookieは不正を防止することができません。
ユーザはセッション内のどんなデータでも変更することができます。
しかし、、、Cookieは簡単で、メモリやデータベースを利用したセッションで
起こるスケーリングの問題がありません。

### メモリベースのセッション

### Memcachedベースのセッション

### ファイルベースのセッション

### データベースベースのセッション


Cookie
-------

CookieをSinatraで利用するのはとても簡単ですが、いくらかクセがあります。

最初に簡単な例を見てみましょう：

    require 'rubygems'
    require 'sinatra'

    get '/' do
        # 文字列表現を得る
        cookie = request.cookies["thing"]

        # デフォルトを設定する
        cookie ||= 0

        # 整数に変換する
        cookie = cookie.to_i

        # 値で何かをする
        cookie += 1

        # cookieをリセットする
        set_cookie("thing", cookie)

        # 何かを表示する
        "Thing is now: #{cookie}"
    end

パス、失効日、ドメインを設定するのは少し複雑です。
詳細はset\_cookieのソースコードを参照してください。

    set_cookie("thing", :domain => myDomain,
                        :path => myPath,
                        :expires => Date.new)

これはCookieの簡単な例です。SinatraではArrayオブジェクトを、
アンパサンド（&amp;）で分割してシリアライズすることもできます。
しかし、それらが戻ってきたとき、自動的にデシリアライズしたり
分割したりはせず、あなたのパースする喜びのために
エンコードされた文字列の生データが渡されます。

ステータス
------

通常の200（Success）ではなく、独自のステータスコードを返したい場合、
`status`ヘルパを使用しステータスコードをセットしながら、
通常通りレンダリングを行うことができます：

    get '/' do
      status 404
      "Not found"
    end

他の方法として、`throw :halt, [404, "Not found"]`を使って、この後の処理をただちにやめて
指定したステータスコードと文字列をクライアントに返すことができます。
この点で`throw`はたくさんのオプションをサポートしています。
詳細は適切な章を参照してください。

認証
--------------

