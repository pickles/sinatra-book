フィルタ
=======

before do...
------------
これらはSinatra::EventContextで実行されます。

    before do
      # .. this code will run before each event ..
    end

Railsのようなネストしたパラメータを扱う (Sinatra <= 0.3.0) {#nested_params_as_filter}
------------------------------------
次のような（Railsのネストしたパラメータとして知られる）パラメータを
もつフォームを使いたい場合：

    <form>
      <input ... name="post[title]" />
      <input ... name="post[body]" />
      <input ... name="post[author]" />
    </form>

パラメータをハッシュに変換する必要があります。
これはbeforeフィルタで簡単に行うことができます。

    before do
      new_params = {}
      params.each_pair do |full_key, value|
        this_param = new_params
        split_keys = full_key.split(/\]\[|\]|\[/)
        split_keys.each_index do |index|
          break if split_keys.length == index + 1
          this_param[split_keys[index]] ||= {}
          this_param = this_param[split_keys[index]]
       end
       this_param[split_keys.last] = value
      end
      request.params.replace new_params
    end

Then parameters became:

    {"post"=>{ "title"=>"", "body"=>"", "author"=>"" }}