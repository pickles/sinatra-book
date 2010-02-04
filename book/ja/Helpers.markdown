ヘルパー
=======

基本
----------

アプリケーションのルートレベルにヘルパーを作るのは良くありません。
グローバルネームスペースを汚し、request、response、session、
cookie変数に簡単にアクセスすることができません。

その代わり、手軽なhelpersメソッドを使用して`Sinatra::EventContext`に
インストールすることでイベントやテンプレートで使用する事ができます。

例：

    helpers do
      def bar(name)
        "#{name}bar"
      end
    end
    
    get '/:name' do
      bar(params[:name])
    end

Railsスタイルのパーシャルの実装
------------------------------------

ビューでパーシャルを使うことは、ビューをきれいに保つ良い方法です。
Sinatraはフレームワークの設計に手をかけない方針を採用しているため、
パーシャルのハンドラを自分で実装しなければなりません。

本当に基本的なバージョンはこのようになります：

    # Usage: partial :foo
    helpers do
      def partial(page, options={})
        haml page, options.merge!(:layout => false)
      end
    end

ローカルオプションを渡したり、ハッシュをループしたりする
より高度なバージョンは次のようになります：

    # Render the page once:
    # Usage: partial :foo
    # 
    # "foo"という名前のローカル変数で渡されたfooは配列の要素ごとにレンダリングされます。
    # Usage: partial :foo, :collection => @my_foos    

    helpers do
      def partial(template, *args)
        options = args.extract_options!
        options.merge!(:layout => false)
        if collection = options.delete(:collection) then
          collection.inject([]) do |buffer, member|
            buffer << haml(template, options.merge(
                                      :layout => false, 
                                      :locals => {template.to_sym => member}
                                    )
                         )
          end.join("\n")
        else
          haml(template, options)
        end
      end
    end
