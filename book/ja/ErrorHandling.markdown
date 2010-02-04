エラーハンドリング
==============

not\_found
---------
注意：これらはSinatra::EventContext内で動作しています。
つまりこれらのすべてのものは提供される必要があります（例えばhaml、erb、:haltなど）

NotFoundが送出されるたび必ずこれが呼び出されます。

    not_found do
      'This is nowhere to be found'
    end

error
-----
デフォルトでは、errorはSinatra::ServerErrorをキャッチします。

Sinatraはrequest.envの'sinatra.error'を介してエラーを渡します。

    error do
      'Sorry there was a nasty error - ' + request.env['sinatra.error'].name
    end
  
カスタムエラーのマッピング：

    error MyCustomError do
      'So what happened was...' + request.env['sinatra.error'].message
    end

このとき、もしこれが起きると：

    get '/' do
      raise MyCustomError, 'something bad'
    end

これを取得します：

    So what happened was... something bad

追加情報
----------------------
Sinatraはデフォルトのnot\_foundとerrorを提供するため、:productionでは安全に実行されます。
もし:productionをカスタマイズし:developmentのフレンドリーな
ヘルパー画面を表示したい場合は次のようにします：

    configure :production do
      not_found do
        "We're so sorry, but we don't what this is"
      end
  
      error do
        "Something really nasty happened.  We're on it!"
      end
    end