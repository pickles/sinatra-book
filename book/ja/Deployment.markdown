Deployment
==========

Heroku
------

これは、もっとも簡単な設定 + デプロイの方法です。

[Heroku]はSinatraアプリケーションを完全にサポートします。
Herokuへのデプロイは単にリモートのgitリポジトリにプッシュするだけです。

Herokuへのデプロイ手順は次のとおりです：

* [アカウント](http://heroku.com/signup)を持っていない場合は作成します
* `sudo gem install heroku`
* ルートディレクトリにconfig.ruファイルを作成します。
* Herokuの上にアプリを作成します。
* そこにプッシュします。

1. config.ruファイルの例（Herokuは`RACK_ENV`をproductionに設定します。）

       require "myapp"

       run Sinatra::Application

2. アプリを作成し、プッシュする

       アプリケーションのルートディレクトリから

       $ heroku create <app-name>  # リモートとしてherokuを追加します
       $ git push heroku master

詳細については、 [ここ](http://github.com/sinatra/heroku-sinatra-app)を参照してください

[Heroku]: http://www.heroku.com

ThinにプロキシしたLighttpd        {#deployment_lighttpd}
------------------------

ここではLighttpdとThinを使ったロードバランシングされた
リバースプロキシのセットにSinatraをデプロイする方法を説明します。

1. LighttpdとThinのインストール

       # Figure out lighttpd yourself, it should be handled by your 
       # linux distro's package manager
        
       # For thin:
       gem install thin

2. rackupファイルの作成 -- `require 'app'`の行で実際のSinatraアプリをrequireします。

       ## This is not needed for Thin > 1.0.0
       ENV['RACK_ENV'] = "production"

       require 'app'
       
       run Sinatra::Application

3. config.ymlの設定 - /path/to/my/appに実際のパスを反映させます。

       ---
         environment: production
         chdir: /path/to/my/app
         address: 127.0.0.1
         user: root
         group: root
         port: 4567
         pid: /path/to/my/app/thin.pid
         rackup: /path/to/my/app/config.ru
         log: /path/to/my/app/thin.log
         max_conns: 1024
         timeout: 30
         max_persistent_conns: 512
         daemonize: true

4. lighttpd.confの設定。mydomainを実際の値に変更します。
   また、ここに記載した最初のポートがconfig.ymlで設定したポートと
   一致していることを確認します。

       $HTTP["host"] =~ "(www\.)?mydomain\.com"  {
               proxy.balance = "fair"
               proxy.server =  ("/" =>
                                       (
                                               ( "host" => "127.0.0.1", "port" => 4567 ),
                                               ( "host" => "127.0.0.1", "port" => 4568 )
                                       )
                               )
       }

5. Thinとアプリケーションを起動します。
   以下のコマンドを実行する代わりに、"rake start"を呼ぶだけで起動することができます。

       thin -s 2 -C config.yml -R config.ru start

これで完了です!mydomain.com/にアクセスして結果をみてください！
これですべての設定ができているはずです。
lighttpd.confファイルで設定したあなたのドメインを確認してください。

*バリエーション* - プロキシ経由でのnginx - nginxウェブサーバに同じ方法でプロキシすることができます。

    upstream www_mydomain_com {
      server 127.0.0.1:5000;
      server 127.0.0.1:5001;
    }
    
    server {
      listen    www.mydomain.com:80
      server_name  www.mydomain.com live;
      access_log /path/to/logfile.log
      
      location / {
        proxy_pass http://www_mydomain_com;
      }
      
    }

*バリエーション* - Thinのインスタンスを増やす - thinの起動コマンドに`-s 2`
パラメータを渡すことで好きな数分thinのインスタンスを増やすことができます
その後、その分のproxy文がlighttpdプロキシに追加されていることを確認してください。
次に、lighttpdを再起動し、期待どおりになっていることを確認します。

Passenger (mod rails)           {#deployment_passenger}
------------------------
FastCGIでのデプロイは嫌いですか？あなただけではありません。
ちょっと聞いてください、PassengerがRackをサポートしています。
そしてこの本はあなたにどうしたらよいかを教えましょう。

より多くのドキュメントをPassengerのGithubリポジトリで見つけることができます。

1. Dreamhostのインターフェイスでのアカウントの設定

       Domains -> Manage Domains -> Edit (web hosting column)
       'Ruby on Rails Passenger (mod_rails)'を有効にする。
       webディレクトリにpublicディレクトリを追加する。
       もし'rails.com'を使っていた場合、'rails.com/public'に変更されています。
       変更を保存します。

2. ディレクトリ構造を作成

       domain.com/
       domain.com/tmp
       domain.com/public
       # gemを使っている場合Sinatraのvendor化されたバージョンは必要ありません。
       domain.com/sinatra

3. （Rackの設定ファイル）"Rackupファイル"である`config.ru`を作成します。
     - `require 'app'`の行はあなたが作成した実際のアプリケーションをrequireしてください。

       ## Passenger should set RACK_ENV for Sinatra

       require 'app'
       
       run Sinatra::Application

4. 非常に単純なSinatraのアプリケーション

       # this is test.rb referred to above
       get '/' do
         "Worked on dreamhost"
       end
        
       get '/foo/:bar' do
         "You asked for foo/#{params[:bar]}"
       end

そしてこれが必要なことのすべてです！
設定が終わったら、あなたのドメインにアクセスすると、
'Worked on Dreamhost'ページが見えるはずです。
変更が終わったあとアプリケーションを再起動するには、
`touch tmp/restart.txt`を実行する必要があります。

現在のPassenger 2.0.3には、Sinatraがviewディレクトリを見つけることが
できなくなるバグが有ることに注意してください。
その場合には、`:views => '/path/to/views/'`
をRackupファイルのSinatraオプションに追加してください。

もしかすると、"can't activeate rack (>= 0.9.1, < 1.0, runtime), 
already activated rack-0.4.0"というメッセージと共に
"Ruby (Rack) application could not be started"という
恐ろしいエラーに遭遇するかもしれません。
これはDreamHostがバージョン 0.4.0をインストールしており、
最近のSinatraのバージョンはもっと新しいバージョンのRackを必要とするために発生します。
これを解決するには、config.ruで明示的にrackとsinatraのgemをrequireする必要があります。
config.ruファイルの先頭に、次の2行を追加します：

       require '/home/USERNAME/.gem/ruby/1.8/gems/rack-VERSION-OF-RACK-GEM-YOU-HAVE-INSTALLELD/lib/rack.rb'
       require '/home/USERNAME/.gem/ruby/1.8/gems/sinatra-VERSION-OF-SINATRA-GEM-YOU-HAVE-INSTALLELD/lib/sinatra.rb'


FastCGI                                        {#deployment_fastcgi}
-------
標準的なデプロイの方法は、Thinか Mongrelを使い、
リバースプロキシ(lighttpd, nginx, またはApache)があなたのサーバを向くようにすることです。

しかし、それはいつもできるとは限りません。（Dreamhostのような）
安い共有ホスティングの場合は、ThinやMongrelを立ち上げることができなかったり、
（少なくともデフォルトの共有プランでは）リバースプロキシの設定ができません。

幸いにも、RackはCGIやFastCGIなどのいくつかのコネクタをサポートしています。
しかし不幸にも現在のSinatraのリリースではいくつかの調整をしないと、FastCGIではうまく動作しません。

### Sinatraのバージョン0.9をデプロイする
Sinatraのバージョン9.0からは Rack0.9.1が必要です。
しかしこのバージョンのFastCGIラッパーでは、あなたのアプリケーションを
 Sinatra::Applicationクラスのサブクラスとして定義し、
Rackアプリケーションとして直接起動しなければうまく動作しません。

FastCGIにデプロイする手順：
* htaccess
* アプリケーションをSinatra::Applicationのサブクラスにする
* dispatch.fcgi

1. .htaccess

        RewriteEngine on
        
        AddHandler fastcgi-script .fcgi
        Options +FollowSymLinks +ExecCGI
        
        RewriteRule ^(.*)$ dispatch.fcgi [QSA,L]

2. アプリケーションを Sinatra::Applicationのサブクラスにする。

        # my_sinatra_app.rb
        class MySinatraApp < Sinatra::Application
          # your sinatra application definitions
        end


3. dispatch.fcgi - アプリケーションを直接Rackアプリケーションとして起動する。

        #!/usr/local/bin/ruby
    
        require 'rubygems'
        require 'rack'
        
        fastcgi_log = File.open("fastcgi.log", "a")
        STDOUT.reopen fastcgi_log
        STDERR.reopen fastcgi_log
        STDOUT.sync = true

        module Rack
          class Request
            def path_info
              @env["REDIRECT_URL"].to_s
            end
            def path_info=(s)
              @env["REDIRECT_URL"] = s.to_s
            end
          end
        end

        load 'my\_sinatra\_app.rb'

        builder = Rack::Builder.new do
          map '/' do
            run MySinatraApp.new
          end
        end

        Rack::Handler::FastCGI.run(builder)


### Sinatraのバージョン0.3以前をデプロイする
バージョン0.3で、単純な'hello world'のSinatraアプリケーションをFastCGIで実行するには、
現在のSinatraコードを取り出し、少しハックする必要があります。
でも心配しないでください、いくつかの行をコメントアウトし、調整するだけです。

デプロイの手順：

* .htaccess
* dispatch.fcgi
* sinatra.rbを調整する


1. .htaccess
       RewriteEngine on
      
       AddHandler fastcgi-script .fcgi
       Options +FollowSymLinks +ExecCGI
      
       RewriteRule ^(.*)$ dispatch.fcgi [QSA,L]

2. dispatch.fcgi
 
       #!/usr/bin/ruby
      
       require 'rubygems'
       require 'sinatra/lib/sinatra'
      
       fastcgi_log = File.open("fastcgi.log", "a")
       STDOUT.reopen fastcgi_log
       STDERR.reopen fastcgi_log
       STDOUT.sync = true
      
       set :logging, false
       set :server, "FastCGI"
      
       module Rack
         class Request
           def path_info
             @env["REDIRECT_URL"].to_s
           end
           def path_info=(s)
             @env["REDIRECT_URL"] = s.to_s
           end
         end
       end
      
       load 'app.rb'

3. sinatra.rb - 新しいバージョンでこの関数を置き換える（`puts`の行をコメントアウトする ）

       def run
         begin
           #puts "== Sinatra has taken the stage on port #{port} for #{env} with backup by #{server.name}"
           require 'pp'
           server.run(application) do |server|
             trap(:INT) do
               server.stop
               #puts "\n== Sinatra has ended his set (crowd applauds)"
             end
           end
         rescue Errno::EADDRINUSE => e
           #puts "== Someone is already performing on port #{port}!"
         end
       end