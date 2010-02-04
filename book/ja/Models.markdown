モデル
======

Datamapper
----------

もしまだ取得していない場合はDataMapperのgemを取得するところから始めてください。
そして、アプリケーションの中にあることを確認してください。
いつも通り`setup`を呼ぶと、ショーの始まりです。次の例は'Post'モデルの例です。

    require 'rubygems'
    require 'sinatra'
    require 'datamapper'
    
    DataMapper::setup(:default, "sqlite3://#{Dir.pwd}/blog.db")
    
    class Post
        include DataMapper::Resource
        property :id, Serial
        property :title, String
        property :body, Text
        property :created_at, DateTime
    end
    
    # postテーブルを自動的につくる
    Post.auto_migrate! unless Post.table_exists?

以上で準備完了です、これで実際にあなたのアプリケーションを開発することができます！

    get '/' do
        # 最新の20ポストを取得する
        @posts = Post.get(:order => [ :id.desc ], :limit => 20)
        erb :index
    end

最後に `./view/index.html`のビューです：

    <% for post in @posts %>
        <h3><%= post.title %></h3>
        <p><%= post.body %></p>
    <% end %>


Sequel
------
Sequel gem が必要です:

    require 'rubygems'
    require 'sinatra'
    require 'sequel'

簡単なインメモリ DBを使います：

    DB = Sequel.sqlite

テーブルを作ります：

    DB.create_table :links do
     primary_key :id
     varchar :title
     varchar :link
    end

モデルクラスを作ります：

    class Link < Sequel::Model
    end

ルートを作ります：
   
    get '/' do
     @links = Link.all
     haml :links
    end

ActiveRecord
------------
最初にアプリケーションでActiveRecord gem をrequireします。
そしてデータベースの接続設定をします：

    require 'rubygems'
    require 'sinatra'
    require 'active_record'

    ActiveRecord::Base.establish_connection(
      :adapter => 'sqlite3',
      :database =>  'sinatra_application.sqlite3.db'
    )

これでRails内のようにActiveRecoredのモデルを作成し使う事ができます。
（この例ではすでに'posts'テーブルがデータベースにあるものとしています）：

    class Post < ActiveRecord::Base
    end
    
    get '/' do
      @posts = Post.all()
      erb :index 
    end
    
これは ./views/index.erbをレンダリングします：

    <% for post in @posts %>
      <h1><%= post.title %></h1>
    <% end %>
