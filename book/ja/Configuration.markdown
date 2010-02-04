設定
=============

Sinatraの"set"オプションを使う
--------------------------

configureブロックはイベントコンテキストで実行されず、
同じインスタンス変数にアクセスすることもありません。
routeでアクセスしたい情報を保存するには`set`を使用してください。

    configure :development do
      set :dbname, 'devdb'
    end

    configure :production do
      set :dbname, 'productiondb'
    end

...

    get '/whatdb' do
      'We are using the database named ' + options.dbname
    end


configureブロックを介した外部の設定ファイル
--------------------------------------------


Application module / config area
--------------------------------

