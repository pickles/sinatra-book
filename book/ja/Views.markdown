ビュー
=====
すべてのファイルベースのビューは次の場所にあります：
All file-based views are looked up in:

    root
      | - views/

テンプレート
------------------

### Haml
    get '/' do
      haml :index
    end

これは./views/index.hamlをレンダリングします。

### Sass
    get '/' do
      sass :styles
    end

これは./views/styles.sassをレンダリングします。

### Erb
    get '/' do
      erb :index
    end

これは./views/index.erbをレンダリングします。

### Builder
    get '/' do
      builder :index
    end

これは./views/index.builderをレンダリングします。

    get '/' do
      builder do |xml|
        xml.node do
          xml.subnode "Inner text"
        end
      end
    end

これはハンドラ内で直接インラインXMLをレンダリングします。

#### Atom Feed

#### RSS Feed
あなたのサイトのURLがhttp://liftoff.msfc.nasa.gov/とした場合。

    get '/rss.xml' do
      builder do |xml|
        xml.instruct! :xml, :version => '1.0'
        xml.rss :version => "2.0" do
          xml.channel do
            xml.title "Liftoff News"
            xml.description "Liftoff to Space Exploration."
            xml.link "http://liftoff.msfc.nasa.gov/"
            
            @posts.each do |post|
              xml.item do
                xml.title post.title
                xml.link "http://liftoff.msfc.nasa.gov/posts/#{post.id}"
                xml.description post.body
                xml.pubDate Time.parse(post.created_at.to_s).rfc822()
                xml.guid "http://liftoff.msfc.nasa.gov/posts/#{post.id}"
              end
            end
          end
        end
      end
    end

これはハンドラ内で直接インラインでRSSをレンダリングします。

レイアウト
-------

Sinatraのレイアウトは簡単です。
"layout.erb"、"layout.haml"または "layout.builder"という名前で
viewsディレクトリに配置してください。ページをレンダリングするとき、
適切な（同じファイルタイプの）レイアウトが選ばれ、使用されます。

コンテンツを含めたい場所で、レイアウト自身で `yield` を呼び出すべきです。
    
Hamlによるレイアウトファイルの例は次のようになります：

    %html
      %head
        %title SINATRA BOOK
      %body
        #container
          = yield

レイアウトを無効化する
-----------------
ときどきレイアウトをレンダリングしたくない場合があります。その場合、
レンダリングするメソッドに、単純に:layout => falseを渡すだけで良いです。

    get '/' do
      haml :index, :layout => false
    end

ファイル内ビュー
-------------

これはステキです：

    get '/' do
      haml :index
    end
    
    use_in_file_templates!
    
    __END__
    
    @@ layout
    X
    = yield
    X
    
    @@ index
    %div.title Hello world!!!!!

試してみてください！

パーシャル
--------

