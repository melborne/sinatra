# Sinatra

*注）
本文書は英語から翻訳したものであり、その内容が最新でない場合もあります。最新の情報はオリジナルの英語版を参照して下さい。*

Sinatraは最小の労力でRubyによるWebアプリケーションを手早く作るための[DSL](http://ja.wikipedia.org/wiki/ドメイン固有言語)です。

``` ruby
# myapp.rb
require 'sinatra'

get '/' do
  'Hello world!'
end
```

gemをインストールし、

``` shell
gem install sinatra
```

次のように実行します。

``` shell
ruby myapp.rb
```

[localhost:4567](http://localhost:4567) を開きます。

ThinがあればSinatraはこれを利用するので、`gem install thin`することをお薦めします。

## ルーティング

Sinatraでは、ルーティングはHTTPメソッドとURLマッチングパターンがペアになっています。
ルーティングはブロックに結び付けられています。

``` ruby
get '/' do
  .. 何か見せる ..
end

post '/' do
  .. 何か生成する ..
end

put '/' do
  .. 何か更新する ..
end

patch '/' do
  .. 何か修正する ..
end

delete '/' do
  .. 何か削除する ..
end

options '/' do
  .. 何か満たす ..
end

link '/' do
  .. 何かリンクを張る ..
end

unlink '/' do
  .. 何かアンリンクする ..
end
```

ルーティングは定義された順番にマッチします。
リクエストに最初にマッチしたルーティングが呼び出されます。

ルーティングのパターンは名前付きパラメータを含むことができ、
`params`ハッシュで取得できます。

``` ruby
get '/hello/:name' do
  # "GET /hello/foo" と "GET /hello/bar" にマッチ
  # params[:name] は 'foo' か 'bar'
  "Hello #{params[:name]}!"
end
```

また、ブロックパラメータで名前付きパラメータにアクセスすることもできます。

``` ruby
get '/hello/:name' do |n|
  # "GET /hello/foo" と "GET /hello/bar" にマッチ
  # params[:name] は 'foo' か 'bar'
  # n が params[:name] を保持
  "Hello #{n}!"
end
```

ルーティングパターンはアスタリスク(すなわちワイルドカード)を含むこともでき、
`params[:splat]` で取得できます。

``` ruby
get '/say/*/to/*' do
  # /say/hello/to/world にマッチ
  params[:splat] # => ["hello", "world"]
end

get '/download/*.*' do
  # /download/path/to/file.xml にマッチ
  params[:splat] # => ["path/to/file", "xml"]
end
```

ブロックパラメータを使用した場合:

``` ruby
get '/download/*.*' do |path, ext|
  [path, ext] # => ["path/to/file", "xml"]
end
```

正規表現を使用した場合:

``` ruby
get %r{/hello/([\w]+)} do
  "Hello, #{params[:captures].first}!"
end
```

ブロックパラメータを使用した場合:

``` ruby
get %r{/hello/([\w]+)} do |c|
  "Hello, #{c}!"
end
```

オプショナルパラメータを使用した場合:

``` ruby
get '/posts.?:format?' do
  # "GET /posts" と "GET /posts.json", "GET /posts.xml" の拡張子などにマッチ
end
```

ところで、ディレクトリトラバーサル保護機能を無効にしないと（下記参照）、
ルーティングにマッチする前にリクエストパスが修正される可能性があります。

### 条件

ルーティングにはユーザエージェントのようなさまざまな条件を含めることができます。

``` ruby
get '/foo', :agent => /Songbird (\d\.\d)[\d\/]*?/ do
  "You're using Songbird version #{params[:agent][0]}"
end

get '/foo' do
  # Matches non-songbird browsers
end
```

ほかに`host_name`と`provides`条件が利用可能です:

``` ruby
get '/', :host_name => /^admin\./ do
  "Admin Area, Access denied!"
end

get '/', :provides => 'html' do
  haml :index
end

get '/', :provides => ['rss', 'atom', 'xml'] do
  builder :feed
end
```

独自の条件を定義することも簡単にできます:

``` ruby
set(:probability) { |value| condition { rand <= value } }

get '/win_a_car', :probability => 0.1 do
  "You won!"
end

get '/win_a_car' do
  "Sorry, you lost."
end
```

### 戻り値

ルーティングブロックの戻り値は、HTTPクライアントまたはRackスタックでの次のミドルウェアに渡されるレスポンスボディを決定します。

これは大抵の場合、上の例のように文字列ですが、それ以外の値も使用することができます。

Rackレスポンス、Rackボディオブジェクト、HTTPステータスコードのいずれかとして
妥当なオブジェクトであればどのようなオブジェクトでも返すことができます:

-   3要素の配列:
    `[ステータス(Fixnum), ヘッダ(Hash), レスポンスボディ(#eachに応答する)]`

-   2要素の配列:
    `[ステータス(Fixnum), レスポンスボディ(#eachに応答する)]`

-   `#each`に応答し、与えられたブロックに文字列を渡すオブジェクト

-   ステータスコードを表現するFixnum

そのように、例えばストリーミングの例を簡単に実装することができます:

``` ruby
class Stream
  def each
    100.times { |i| yield "#{i}\n" }
  end
end

get('/') { Stream.new }
```

後述する`stream`ヘルパーメソッドを使って、定型パターンを減らしつつストリーミングロジックをルーティングに埋め込むこともできます。

## カスタムルーティングマッチャー

先述のようにSinatraはルーティングマッチャーとして、文字列パターンと正規表現を使うことをビルトインでサポートしています。しかしこれに留まらず、独自のマッチャーを簡単に定義することもできるのです。

``` ruby
class AllButPattern
  Match = Struct.new(:captures)

  def initialize(except)
    @except   = except
    @captures = Match.new([])
  end

  def match(str)
    @captures unless @except === str
  end
end

def all_but(pattern)
  AllButPattern.new(pattern)
end

get all_but("/index") do
  # ...
end
```

ノート: この例はオーバースペックであり、以下のようにも書くことができます。

``` ruby
get // do
  pass if request.path_info == "/index"
  # ...
end
```

または、否定先読みを使って:

``` ruby
get %r{^(?!/index$)} do
  # ...
end
```


## 静的ファイル

静的ファイルは`./public`ディレクトリから配信されます。
`:public_folder`オプションを指定することで別の場所を指定することができます。

``` ruby
set :public_folder, File.dirname(__FILE__) + '/static'
```

ノート: この静的ファイル用のディレクトリ名はURL中に含まれません。
例えば、`./public/css/style.css`は`http://example.com/css/style.css`でアクセスできます。

## ビュー / テンプレート

各テンプレート言語はそれ自身のレンダリングメソッドを通して展開されます。それらのメソッドは単に文字列を返します。

``` ruby
get '/' do
  erb :index
end
```

これは、`views/index.erb`をレンダリングします。

テンプレート名を渡す代わりに、直接そのテンプレートの中身を渡すこともできます。

``` ruby
get '/' do
  code = "<%= Time.now %>"
  erb code
end
```

テンプレートはハッシュオプションの第２引数を取ります。

``` ruby
get '/' do
  erb :index, :layout => :post
end
```

これは、`views/post.erb`内に埋め込んだ`views/index.erb`をレンダリングします（デフォルトは`views/layout.erb`があればそれになります）。

Sinatraが理解できないオプションは、テンプレートエンジンに渡されることになります。


``` ruby
get '/' do
  haml :index, :format => :html5
end
```

テンプレート言語ごとにオプションをセットすることもできます。

``` ruby
set :haml, :format => :html5

get '/' do
  haml :index
end
```

レンダリングメソッドに渡されたオプションは`set`で設定されたオプションを上書きします。

利用可能なオプション:

<dl>
  <dt>locals</dt>
  <dd>
    ドキュメントに渡されるローカルのリスト。パーシャルに便利。
    例: <tt>erb "<%= foo %>", :locals => {:foo => "bar"}</tt>
  </dd>

  <dt>default_encoding</dt>
  <dd>
    文字エンコーディング（不確かな場合に使用される）。デフォルトは、<tt>settings.default_encoding</tt>。
  </dd>

  <dt>views</dt>
  <dd>
    テンプレートを読み出すビューのディレクトリ。デフォルトは、<tt>settings.views</tt>。
  </dd>

  <dt>layout</dt>
  <dd>
    レイアウトを使うかの指定(<tt>true</tt> または <tt>false</tt>)。値がシンボルの場合は、使用するテンプレートが指定される。例: <tt>erb :index, :layout => !request.xhr?</tt>
  </dd>

  <dt>content_type</dt>
  <dd>
    テンプレートが生成するContent-Type。デフォルトはテンプレート言語ごとに異なる。
  </dd>

  <dt>scope</dt>
  <dd>
    テンプレートをレンダリングするときのスコープ。デフォルトは、アプリケーションのインスタンス。これを変更した場合、インスタンス変数およびヘルパーメソッドが利用できなくなる。
  </dd>

  <dt>layout_engine</dt>
  <dd>
    レイアウトをレンダリングするために使用するテンプレートエンジン。レイアウトをサポートしない言語で有用。デフォルトはテンプレートに使われるエンジン。例: <tt>set :rdoc, :layout_engine => :erb</tt>
  </dd>

  <dt>layout_options</dt>
  <dd>
    レイアウトをレンダリングするときだけに使う特別なオプション。例:
    <tt>set :rdoc, :layout_options => { :views => 'views/layouts' }</tt>
  </dd>
</dl>

テンプレートは`./views`ディレクトリ下に配置されています。
他のディレクトリを使用する場合の例:

``` ruby
set :views, File.dirname(__FILE__) + '/templates'
```

テンプレートはシンボルを使用して参照させることを覚えておいて下さい。
サブデレクトリでもこの場合は`:'subdir/template'`のようにします。
レンダリングメソッドは文字列が渡されると、それをそのまま文字列として出力するので、シンボルを使ってください。

### リテラルテンプレート

``` ruby
get '/' do
  haml '%div.title Hello World'
end
```

これはそのテンプレート文字列をレンダリングします。

### 利用可能なテンプレート言語

いくつかの言語には複数の実装があります。使用する（そしてスレッドセーフにする）実装を指定するには、それを最初にrequireしてください。


``` ruby
require 'rdiscount' # または require 'bluecloth'
get('/') { markdown :index }
```

#### Haml テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://haml.info/" title="haml">haml</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.haml</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>haml :index, :format => :html5</tt></td>
  </tr>
</table>


#### Erb テンプレート

<table>
  <tr>
    <td>依存</td>
    <td>
      <a href="http://www.kuwata-lab.com/erubis/" title="erubis">erubis</a>
      または erb (Rubyに同梱)
    </td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.erb</tt>, <tt>.rhtml</tt> or <tt>.erubis</tt> (Erubis only)</td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>erb :index</tt></td>
  </tr>
</table>

#### Builder テンプレート

<table>
  <tr>
    <td>依存</td>
    <td>
      <a href="http://builder.rubyforge.org/" title="builder">builder</a>
    </td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.builder</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>builder { |xml| xml.em "hi" }</tt></td>
  </tr>
</table>

インラインテンプレート用にブロックを取ることもできます（例を参照）。

#### Nokogiri テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://nokogiri.org/" title="nokogiri">nokogiri</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.nokogiri</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>nokogiri { |xml| xml.em "hi" }</tt></td>
  </tr>
</table>

インラインテンプレート用にブロックを取ることもできます（例を参照）。


#### Sass テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://sass-lang.com/" title="sass">sass</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.sass</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>sass :stylesheet, :style => :expanded</tt></td>
  </tr>
</table>


#### Scss テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://sass-lang.com/" title="sass">sass</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.scss</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>scss :stylesheet, :style => :expanded</tt></td>
  </tr>
</table>

#### Less テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://www.lesscss.org/" title="less">less</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.less</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>less :stylesheet</tt></td>
  </tr>
</table>

#### Liquid テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://www.liquidmarkup.org/" title="liquid">liquid</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.liquid</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>liquid :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

LiquidテンプレートからRubyのメソッド(`yield`を除く)を呼び出すことができないため、ほぼ全ての場合にlocalsを指定する必要があるでしょう:

#### Markdown テンプレート

<table>
  <tr>
    <td>依存</td>
    <td>
      次の何れか:
        <a href="https://github.com/rtomayko/rdiscount" title="RDiscount">RDiscount</a>,
        <a href="https://github.com/vmg/redcarpet" title="RedCarpet">RedCarpet</a>,
        <a href="http://deveiate.org/projects/BlueCloth" title="BlueCloth">BlueCloth</a>,
        <a href="http://kramdown.rubyforge.org/" title="kramdown">kramdown</a>,
        <a href="http://maruku.rubyforge.org/" title="maruku">maruku</a>
    </td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.markdown</tt>, <tt>.mkd</tt> and <tt>.md</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>markdown :index, :layout_engine => :erb</tt></td>
  </tr>
</table>

Markdownからメソッドを呼び出すことも、localsに変数を渡すこともできません。
それゆえ、他のレンダリングエンジンとの組み合わせで使うのが普通です:

``` ruby
erb :overview, :locals => { :text => markdown(:introduction) }
```

ノート: 他のテンプレート内で`markdown`メソッドを呼び出せます。

``` ruby
%h1 Hello From Haml!
%p= markdown(:greetings)
```

MarkdownからはRubyを呼ぶことができないので、Markdownで書かれたレイアウトを使うことはできません。しかしながら、`:layout_engine`オプションを渡すことでテンプレートのものとは異なるレンダリングエンジンをレイアウトのために使うことができます。


#### Textile テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://redcloth.org/" title="RedCloth">RedCloth</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.textile</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>textile :index, :layout_engine => :erb</tt></td>
  </tr>
</table>

Textileからメソッドを呼び出すことも、localsに変数を渡すこともできません。
それゆえ、他のレンダリングエンジンとの組み合わせで使うのが普通です:

``` ruby
erb :overview, :locals => { :text => textile(:introduction) }
```

ノート: 他のテンプレート内で`textile`メソッドを呼び出せます。

``` ruby
%h1 Hello From Haml!
%p= textile(:greetings)
```

TexttileからはRubyを呼ぶことができないので、Textileで書かれたレイアウトを使うことはできません。しかしながら、`:layout_engine`オプションを渡すことでテンプレートのものとは異なるレンダリングエンジンをレイアウトのために使うことができます。

``` ruby
erb :overview, :locals => { :text => textile(:introduction) }
```

#### RDoc テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://rdoc.rubyforge.org/" title="RDoc">RDoc</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.rdoc</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>rdoc :README, :layout_engine => :erb</tt></td>
  </tr>
</table>

RDocからメソッドを呼び出すことも、localsに変数を渡すこともできません。
それゆえ、他のレンダリングエンジンとの組み合わせで使うのが普通です:

``` ruby
erb :overview, :locals => { :text => rdoc(:introduction) }
```

ノート: 他のテンプレート内で`rdoc`メソッドを呼び出せます。


``` ruby
%h1 Hello From Haml!
%p= rdoc(:greetings)
```

RDocからはRubyを呼ぶことができないので、RDocで書かれたレイアウトを使うことはできません。しかしながら、`:layout_engine`オプションを渡すことでテンプレートのものとは異なるレンダリングエンジンをレイアウトのために使うことができます。

#### Radius テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://radius.rubyforge.org/" title="Radius">Radius</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.radius</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>radius :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

RadiusテンプレートからRubyのメソッドを直接呼び出すことができないため、ほぼ全ての場合にlocalsを指定する必要があるでしょう。


#### Markaby テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://markaby.github.com/" title="Markaby">Markaby</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.mab</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>markaby { h1 "Welcome!" }</tt></td>
  </tr>
</table>

インラインテンプレート用にブロックを取ることもできます（例を参照）。

#### RABL テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="https://github.com/nesquena/rabl" title="Rabl">Rabl</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.rabl</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>rabl :index</tt></td>
  </tr>
</table>

#### Slim テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="http://slim-lang.com/" title="Slim Lang">Slim Lang</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.slim</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>slim :index</tt></td>
  </tr>
</table>

#### Creole テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="https://github.com/minad/creole" title="Creole">Creole</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.creole</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>creole :wiki, :layout_engine => :erb</tt></td>
  </tr>
</table>

Creoleからメソッドを呼び出すことも、localsに変数を渡すこともできません。
それゆえ、他のレンダリングエンジンとの組み合わせで使うのが普通です:

``` ruby
erb :overview, :locals => { :text => creole(:introduction) }
```

ノート: 他のテンプレート内で`creole`メソッドを呼び出せます。

``` ruby
%h1 Hello From Haml!
%p= creole(:greetings)
```

CreoleからはRubyを呼ぶことができないので、Creoleで書かれたレイアウトを使うことはできません。しかしながら、`:layout_engine`オプションを渡すことでテンプレートのものとは異なるレンダリングエンジンをレイアウトのために使うことができます。

#### CoffeeScript テンプレート

<table>
  <tr>
    <td>依存</td>
    <td>
      <a href="https://github.com/josh/ruby-coffee-script" title="Ruby CoffeeScript">
        CoffeeScript
      </a> および 
      <a href="https://github.com/sstephenson/execjs/blob/master/README.md#readme" title="ExecJS">
        JavaScriptの起動方法
      </a>
    </td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.coffee</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>coffee :index</tt></td>
  </tr>
</table>

#### Stylus テンプレート

<table>
  <tr>
    <td>依存</td>
    <td>
      <a href="https://github.com/lucasmazza/ruby-stylus" title="Ruby Stylus">
        Stylus
      </a> および 
      <a href="https://github.com/sstephenson/execjs/blob/master/README.md#readme" title="ExecJS">
        JavaScriptの起動方法
      </a>
    </td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.styl</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>stylus :index</tt></td>
  </tr>
</table>

Stylusテンプレートを使えるようにする前に、まず`stylus`と`stylus/tilt`を読み込む必要があります。

``` ruby
require 'sinatra'
require 'stylus'
require 'stylus/tilt'

get '/' do
  stylus :example
end
```

#### Yajl テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="https://github.com/brianmario/yajl-ruby" title="yajl-ruby">yajl-ruby</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.yajl</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td>
      <tt>
        yajl :index,
             :locals => { :key => 'qux' },
             :callback => 'present',
             :variable => 'resource'
      </tt>
    </td>
  </tr>
</table>


テンプレートのソースはRubyの文字列として評価され、その結果のJSON変数は`#to_json`を使って変換されます。

``` ruby
json = { :foo => 'bar' }
json[:baz] = key
```

`:callback`および`:variable`オプションは、レンダリングされたオブジェクトを装飾するために使うことができます。

``` ruby
var resource = {"foo":"bar","baz":"qux"}; present(resource);
```

#### WLang テンプレート

<table>
  <tr>
    <td>依存</td>
    <td><a href="https://github.com/blambeau/wlang/" title="wlang">wlang</a></td>
  </tr>
  <tr>
    <td>ファイル拡張子</td>
    <td><tt>.wlang</tt></td>
  </tr>
  <tr>
    <td>例</td>
    <td><tt>wlang :index, :locals => { :key => 'value' }</tt></td>
  </tr>
</table>

WLang内でのRubyメソッドの呼び出しは一般的ではないので、ほとんどの場合にlocalsを指定する必要があるでしょう。しかしながら、WLangで書かれたレイアウトは`yield`をサポートしています。

### テンプレート内で変数にアクセスする

テンプレートはルーティングハンドラと同じコンテキストの中で評価されます。ルーティングハンドラでセットされたインスタンス変数はテンプレート内で直接使うことができます。

``` ruby
get '/:id' do
  @foo = Foo.find(params[:id])
  haml '%h1= @foo.name'
end
```

また、ローカル変数のハッシュで明示的に指定することもできます。

``` ruby
get '/:id' do
  foo = Foo.find(params[:id])
  haml '%h1= bar.name', :locals => { :bar => foo }
end
```

このやり方は他のテンプレート内で部分テンプレートとして表示する時に典型的に使用されます。

### `yield`を伴うテンプレートとネストしたレイアウト

レイアウトは通常`yield`を呼ぶ単なるテンプレートに過ぎません。
そのようなテンプレートは、既に説明した`:template`オプションを通して使われるか、または次のようなブロックを伴ってレンダリングされます。

``` ruby
erb :post, :layout => false do
  erb :index
end
```

このコードは、`erb :index, :layout => :post`とほぼ等価です。

レンダリングメソッドにブロックを渡すスタイルは、ネストしたレイアウトを作るために最も役立ちます。

``` ruby
erb :main_layout, :layout => false do
  erb :admin_layout do
    erb :user
  end
end
```

これはまた次のより短いコードでも達成できます。

``` ruby
erb :admin_layout, :layout => :main_layout do
  erb :user
end
```

現在、次のレンダリングメソッドがブロックを取れます: `erb`, `haml`,
`liquid`, `slim `, `wlang`.
また汎用の`render`メソッドもブロックを取れます。


### インラインテンプレート

テンプレートはソースファイルの最後で定義することもできます。

``` ruby
require 'rubygems'
require 'sinatra'

get '/' do
  haml :index
end

__END__

@@ layout
%html
  = yield

@@ index
%div.title Hello world!!!!!
```

ノート: sinatraをrequireするソースファイル内で定義されたインラインテンプレートは自動的に読み込まれます。他のソースファイル内にインラインテンプレートがある場合には`enable :inline_templates`を明示的に呼んでください。

### 名前付きテンプレート

テンプレートはトップレベルの`template`メソッドで定義することもできます。

``` ruby
template :layout do
  "%html\n  =yield\n"
end

template :index do
  '%div.title Hello World!'
end

get '/' do
  haml :index
end
```

「layout」というテンプレートが存在する場合、そのテンプレートファイルは他のテンプレートがレンダリングされる度に使用されます。`:layout => false`で個別に、または`set :haml, :layout => false`でデフォルトとして、レイアウトを無効にすることができます。

``` ruby
get '/' do
  haml :index, :layout => !request.xhr?
end
```

### ファイル拡張子の関連付け

任意のテンプレートエンジンにファイル拡張子を関連付ける場合は、`Tilt.register`を使います。例えば、Textileテンプレートに`tt`というファイル拡張子を使いたい場合は、以下のようにします。

``` ruby
Tilt.register :tt, Tilt[:textile]
```

### オリジナルテンプレートエンジンの追加

まず、Tiltでそのエンジンを登録し、次にレンダリングメソッドを作ります。

``` ruby
Tilt.register :myat, MyAwesomeTemplateEngine

helpers do
  def myat(*args) render(:myat, *args) end
end

get '/' do
  myat :index
end
```

これは、`./views/index.myat`をレンダリングします。Tiltについての詳細は、https://github.com/rtomayko/tilt を参照してください。

## フィルタ

beforeフィルタは、リクエストのルーティングと同じコンテキストで各リクエストの前に評価され、それによってリクエストとレスポンスを変更可能にします。フィルタ内でセットされたインスタンス変数はルーティングとテンプレートからアクセスすることができます。

``` ruby
before do
  @note = 'Hi!'
  request.path_info = '/foo/bar/baz'
end

get '/foo/*' do
  @note #=> 'Hi!'
  params[:splat] #=> 'bar/baz'
end
```

afterフィルタは、リクエストのルーティングと同じコンテキストで各リクエストの後に評価され、それによってこれもリクエストとレスポンスを変更可能にします。beforeフィルタとルーティング内でセットされたインスタンス変数はafterフィルタからアクセスすることができます。

``` ruby
after do
  puts response.status
end
```

ノート: `body`メソッドを使わずにルーティングから文字列を返すだけの場合、その内容はafterフィルタでまだ利用できず、その後に生成されることになります。

フィルタにはオプションとしてパターンを渡すことができ、この場合はリクエストのパスがパターンにマッチした場合にのみフィルタが評価されるようになります。

``` ruby
before '/protected/*' do
  authenticate!
end

after '/create/:slug' do |slug|
  session[:last_slug] = slug
end
```

ルーティング同様、フィルタもまた条件を取ることができます。

``` ruby
before :agent => /Songbird/ do
  # ...
end

after '/blog/*', :host_name => 'example.com' do
  # ...
end
```

## ヘルパー

トップレベルの`helpers`メソッドを使用してルーティングハンドラやテンプレートで使うヘルパーメソッドを定義できます。

``` ruby
helpers do
  def bar(name)
    "#{name}bar"
  end
end

get '/:name' do
  bar(params[:name])
end
```

あるいは、ヘルパーメソッドをモジュール内で個別に定義することもできます。

``` ruby
module FooUtils
  def foo(name) "#{name}foo" end
end

module BarUtils
  def bar(name) "#{name}bar" end
end

helpers FooUtils, BarUtils
```

その効果は、アプリケーションクラスにモジュールをインクルードするのと同じです。


### セッションの使用

セッションはリクエスト間での状態維持のために使用されます。その起動により、ユーザセッションごとに一つのセッションハッシュが与えられます。

``` ruby
enable :sessions

get '/' do
  "value = " << session[:value].inspect
end

get '/:value' do
  session[:value] = params[:value]
end
```

ノート: `enable :sessions`は実際にはすべてのデータをクッキーに保持します。これは必ずしも期待通りのものにならないかもしれません（例えば、大量のデータを保持することでトラフィックが増大するなど）。Rackセッションmiddlewareの利用が可能であり、その場合は`enable :sessions`を呼ばずに、選択したmiddlewareを他のmiddlewareのときと同じようにして取り込んでください。

``` ruby
use Rack::Session::Pool, :expire_after => 2592000

get '/' do
  "value = " << session[:value].inspect
end

get '/:value' do
  session[:value] = params[:value]
end
```

セキュリティ向上のため、クッキー内のセッションデータはセッションシークレットで署名されます。Sinatraによりランダムなシークレットが個別に生成されます。しかし、このシークレットはアプリケーションの立ち上げごとに変わってしまうので、すべてのアプリケーションのインスタンスで共有できるシークレットをセットしたくなるかもしれません。

``` ruby
set :session_secret, 'super secret'
```

更に、設定変更をしたい場合は、`sessions`の設定においてオプションハッシュを保持することもできます。

``` ruby
set :sessions, :domain => 'foo.com'
```

foo.comのサブドメイン上のアプリ間でセッションを共有化したいときは、代わりにドメインの前に*.*を付けます。

``` ruby
set :sessions, :domain => '.foo.com'
```

### 停止

フィルタまたはルーティング内で直ちに実行を終了する方法:

``` ruby
halt
```

この際、ステータスを指定することもできます。

``` ruby
halt 410
```

body部を指定することも、

``` ruby
halt 'ここにbodyを書く'
```

ステータスとbody部を指定することも、

``` ruby
halt 401, '立ち去れ!'
```

ヘッダを付けることもできます。

``` ruby
halt 402, {'Content-Type' => 'text/plain'}, 'リベンジ'
```

もちろん、テンプレートを`halt`に結びつけることも可能です。

``` ruby
halt erb(:error)
```

### パッシング(Passing)

ルーティングは`pass`を使って次のルーティングに飛ばすことができます:

``` ruby
get '/guess/:who' do
  pass unless params[:who] == 'Frank'
  "見つかっちゃった!"
end

get '/guess/*' do
  "はずれです!"
end
```

ルーティングブロックからすぐに抜け出し、次にマッチするルーティングを実行します。マッチするルーティングが見当たらない場合は404が返されます。

## リクエストオブジェクトへのアクセス

受信するリクエストオブジェクトは、\`request\`メソッドを通じてリクエストレベル(フィルタ、ルーティング、エラーハンドラ)からアクセスすることができます:

``` ruby
# アプリケーションが http://example.com/example で動作している場合
get '/foo' do
  request.body              # クライアントによって送信されたリクエストボディ(下記参照)
  request.scheme            # "http"
  request.script_name       # "/example"
  request.path_info         # "/foo"
  request.port              # 80
  request.request_method    # "GET"
  request.query_string      # ""
  request.content_length    # request.bodyの長さ
  request.media_type        # request.bodyのメディアタイプ
  request.host              # "example.com"
  request.get?              # true (他の動詞についても同様のメソッドあり)
  request.form_data?        # false
  request["SOME_HEADER"]    # SOME_HEADERヘッダの値
  request.referer           # クライアントのリファラまたは'/'
  request.user_agent        # ユーザエージェント (:agent 条件によって使用される)
  request.cookies           # ブラウザクッキーのハッシュ
  request.xhr?              # Ajaxリクエストかどうか
  request.url               # "http://example.com/example/foo"
  request.path              # "/example/foo"
  request.ip                # クライアントのIPアドレス
  request.secure?           # false
  request.env               # Rackによって渡された生のenvハッシュ
end
```

`script_name`や`path_info`などのオプションは次のように利用することもできます:

``` ruby
before { request.path_info = "/" }

get "/" do
  "全てのリクエストはここに来る"
end
```

`request.body`はIOまたはStringIOのオブジェクトです:

``` ruby
post "/api" do
  request.body.rewind  # 既に読まれているときのため
  data = JSON.parse request.body.read
  "Hello #{data['name']}!"
end
```

## 設定

どの環境でも起動時に１回だけ実行されます。

``` ruby
configure do
  ...
end
```

環境(RACK\_ENV環境変数)が`:production`に設定されている時だけ実行する方法:

``` ruby
configure :production do
  ...
end
```

環境が`:production`か`:test`の場合に設定する方法:

``` ruby
configure :production, :test do
  ...
end
```

## エラーハンドリング

エラーハンドラーはルーティングコンテキストとbeforeフィルタ内で実行します。
`haml`、`erb`、`halt`などを使うこともできます。

### Not Found

`Sinatra::NotFound`が起きた時か レスポンスのステータスコードが
404の時に`not_found`ハンドラーが発動します。

``` ruby
not_found do
  'ファイルが存在しません'
end
```

### エラー

`error`
ハンドラーはルーティングブロックかbeforeフィルタ内で例外が発生した時はいつでも発動します。
例外オブジェクトはRack変数`sinatra.error`から取得できます。

``` ruby
error do
  'エラーが発生しました。 - ' + env['sinatra.error'].name
end
```

エラーをカスタマイズする場合は、

``` ruby
error MyCustomError do
  'エラーメッセージ...' + env['sinatra.error'].message
end
```

と書いておいて,下記のように呼び出します。

``` ruby
get '/' do
  raise MyCustomError, '何かがまずかったようです'
end
```

そうするとこうなります:

```
エラーメッセージ... 何かがまずかったようです
```

あるいは、ステータスコードに対応するエラーハンドラを設定することもできます:

``` ruby
error 403 do
  'Access forbidden'
end

get '/secret' do
  403
end
```

範囲指定もできます:

``` ruby
error 400..510 do
  'Boom'
end
```

開発環境として実行している場合、Sinatraは特別な`not_found`と`error`ハンドラーを
インストールしています。

## MIMEタイプ

`send_file`か静的ファイルを使う時、Sinatraが理解でいないMIMEタイプがある場合があります。
その時は `mime_type` を使ってファイル拡張子毎に登録して下さい。

``` ruby
mime_type :foo, 'text/foo'
```

これはcontent\_typeヘルパーで利用することができます:

``` ruby
content_type :foo
```

## Rackミドルウェア

[SinatraはRack](http://rack.rubyforge.org/)フレームワーク用の
最小限の標準インターフェース
上で動作しています。Rack中でもアプリケーションデベロッパー
向けに一番興味深い機能はミドルウェア(サーバとアプリケーション間に介在し、モニタリング、HTTPリクエストとレスポンス
の手動操作ができるなど、一般的な機能のいろいろなことを提供するもの)をサポートすることです。

Sinatraではトップレベルの`use`
メソッドを使ってRackにパイプラインを構築します。

``` ruby
require 'sinatra'
require 'my_custom_middleware'

use Rack::Lint
use MyCustomMiddleware

get '/hello' do
  'Hello World'
end
```

`use`
[Rack::Builder](http://rack.rubyforge.org/doc/classes/Rack/Builder.html)
DSLで定義されていることと全て一致します。 例えば `use`
メソッドはブロック構文のように複数の引数を受け取ることができます。

``` ruby
use Rack::Auth::Basic do |username, password|
  username == 'admin' && password == 'secret'
end
```

Rackはログ、デバッギング、URLルーティング、認証、セッションなどいろいろな機能を備えた標準的ミドルウェアです。
Sinatraはその多くのコンポーネントを自動で使うよう基本設定されているため、`use`で明示的に指定する必要はありません。

## テスト

SinatraでのテストはRack-basedのテストライブラリかフレームワークを使って書くことができます。
[Rack::Test](http://gitrdoc.com/brynary/rack-test)
をおすすめします。やり方:

``` ruby
require 'my_sinatra_app'
require 'rack/test'

class MyAppTest < Test::Unit::TestCase
  include Rack::Test::Methods

  def app
    Sinatra::Application
  end

  def test_my_default
    get '/'
    assert_equal 'Hello World!', last_response.body
  end

  def test_with_params
    get '/meet', :name => 'Frank'
    assert_equal 'Hello Frank!', last_response.body
  end

  def test_with_rack_env
    get '/', {}, 'HTTP_USER_AGENT' => 'Songbird'
    assert_equal "あなたはSongbirdを使ってますね!", last_response.body
  end
end
```

注意: ビルトインのSinatra::TestモジュールとSinatra::TestHarnessクラスは
0.9.2リリース以降、廃止予定になっています。

## Sinatra::Base - ミドルウェア、ライブラリ、 モジュラーアプリ

トップレベル(グローバル領域)上でいろいろ定義していくのは軽量アプリならうまくいきますが、
RackミドルウェアやRails metal、サーバのコンポーネントを含んだシンプルな
ライブラリやSinatraの拡張プログラムを考慮するような場合はそうとは限りません。
トップレベルのDSLがネームスペースを汚染したり、設定を変えてしまうこと(例:./publicや./view)がありえます。
そこでSinatra::Baseの出番です。

``` ruby
require 'sinatra/base'

class MyApp < Sinatra::Base
  set :sessions, true
  set :foo, 'bar'

  get '/' do
    'Hello world!'
  end
end
```

このMyAppは独立したRackコンポーネントで、RackミドルウェアやRackアプリケーション
Rails metalとして使用することができます。`config.ru`ファイル内で `use`
か、または `run`
でこのクラスを指定するか、ライブラリとしてサーバコンポーネントをコントロールします。

``` ruby
MyApp.run! :host => 'localhost', :port => 9090
```

Sinatra::Baseのサブクラスで使えるメソッドはトップレベルのDSLを経由して確実に使うことができます。
ほとんどのトップレベルで記述されたアプリは、以下の２点を修正することでSinatra::Baseコンポーネントに変えることができます。

-   `sinatra`の代わりに`sinatra/base`を読み込む

(そうしない場合、SinatraのDSLメソッドの全てがメインネームスペースにインポートされます)

-   ルーティング、エラーハンドラー、フィルター、オプションをSinatra::Baseのサブクラスに書く

`Sinatra::Base`
はまっさらです。ビルトインサーバを含む、ほとんどのオプションがデフォルト
で無効になっています。オプション詳細については[Options and
Configuration](http://sinatra.github.com/configuration.html)
をご覧下さい。

補足:
SinatraのトップレベルDSLはシンプルな委譲(delgation)システムで実装されています。
`Sinatra::Application`クラス(Sinatra::Baseの特別なサブクラス)は、トップレベルに送られる
:get、 :put、 :post、:delete、 :before、:error、:not\_found、
:configure、:set messagesのこれら 全てを受け取ります。
詳細を閲覧されたい方はこちら(英語): [Sinatra::Delegator
mixin](http://github.com/sinatra/sinatra/blob/master/lib/sinatra/base.rb#L1064)
[included into the main
namespace](http://github.com/sinatra/sinatra/blob/master/lib/sinatra/main.rb#L25).

### Sinatraをミドルウェアとして利用する

Sinatraは他のRackミドルウェアを利用することができるだけでなく、
全てのSinatraアプリケーションは、それ自体ミドルウェアとして別のRackエンドポイントの前に追加することが可能です。

このエンドポイントには、別のSinatraアプリケーションまたは他のRackベースのアプリケーション(Rails/Ramaze/Camping/…)が用いられるでしょう。

``` ruby
require 'sinatra/base'

class LoginScreen < Sinatra::Base
  enable :sessions

  get('/login') { haml :login }

  post('/login') do
    if params[:name] = 'admin' and params[:password] = 'admin'
      session['user_name'] = params[:name]
    else
      redirect '/login'
    end
  end
end

class MyApp < Sinatra::Base
  # middleware will run before filters
  use LoginScreen

  before do
    unless session['user_name']
      halt "Access denied, please <a href='/login'>login</a>."
    end
  end

  get('/') { "Hello #{session['user_name']}." }
end
```

## スコープとバインディング

現在のスコープはどのメソッドや変数が利用可能かを決定します。

### アプリケーション/クラスのスコープ

全てのSinatraアプリケーションはSinatra::Baseのサブクラスに相当します。
もしトップレベルDSLを利用しているならば(`require 'sinatra'`)このクラスはSinatra::Applicationであり、
そうでなければ、あなたが明示的に作成したサブクラスです。
クラスレベルでは\`get\`や\`before\`のようなメソッドを持っています。
しかし\`request\`オブジェクトや\`session\`には、全てのリクエストのために1つのアプリケーションクラスが存在するためアクセスできません。

\`set\`によって作られたオプションはクラスレベルのメソッドです:

``` ruby
class MyApp < Sinatra::Base
  # Hey, I'm in the application scope!
  set :foo, 42
  foo # => 42

  get '/foo' do
    # Hey, I'm no longer in the application scope!
  end
end
```

次の場所ではアプリケーションスコープバインディングを持ちます:

-   アプリケーションのクラス本体

-   拡張によって定義されたメソッド

-   \`helpers\`に渡されたブロック

-   \`set\`の値として使われるProcまたはブロック

このスコープオブジェクト(クラス)は次のように利用できます:

-   configureブロックに渡されたオブジェクト経由(`configure { |c| ... }`)

-   リクエストスコープの中での\`settings\`

### リクエスト/インスタンスのスコープ

やってくるリクエストごとに、あなたのアプリケーションクラスの新しいインスタンスが作成され、全てのハンドラブロックがそのスコープで実行されます。
このスコープの内側からは\`request\`や\`session\`オブジェクトにアクセスすることができ、\`erb\`や\`haml\`のような表示メソッドを呼び出すことができます。
リクエストスコープの内側からは、\`settings\`ヘルパーによってアプリケーションスコープにアクセスすることができます。

``` ruby
class MyApp < Sinatra::Base
  # Hey, I'm in the application scope!
  get '/define_route/:name' do
    # Request scope for '/define_route/:name'
    @value = 42

    settings.get("/#{params[:name]}") do
      # Request scope for "/#{params[:name]}"
      @value # => nil (not the same request)
    end

    "Route defined!"
  end
end
```

次の場所ではリクエストスコープバインディングを持ちます:

-   get/head/post/put/delete ブロック

-   before/after フィルタ

-   helper メソッド

-   テンプレート/ビュー

### デリゲートスコープ

デリゲートスコープは、単にクラススコープにメソッドを転送します。
しかしながら、クラスのバインディングを持っていないため、クラススコープと全く同じふるまいをするわけではありません:
委譲すると明示的に示されたメソッドのみが利用可能であり、またクラススコープと変数/状態を共有することはできません(注:
異なった\`self\`を持っています)。
`Sinatra::Delegator.delegate :method_name`を呼び出すことによってデリゲートするメソッドを明示的に追加することができます。

次の場所ではデリゲートスコープを持ちます:

-   もし`require "sinatra"`しているならば、トップレベルバインディング

-   \`Sinatra::Delegator\` mixinでextendされたオブジェクト

コードをご覧ください: ここでは [Sinatra::Delegator
mixin](http://github.com/sinatra/sinatra/blob/ceac46f0bc129a6e994a06100aa854f606fe5992/lib/sinatra/base.rb#L1128)
は[main
名前空間にincludeされています](http://github.com/sinatra/sinatra/blob/ceac46f0bc129a6e994a06100aa854f606fe5992/lib/sinatra/main.rb#L28).

## コマンドライン

Sinatraアプリケーションは直接実行できます。

``` shell
ruby myapp.rb [-h] [-x] [-e ENVIRONMENT] [-p PORT] [-o HOST] [-s HANDLER]
```

オプション:

    -h # ヘルプ
    -p # ポート指定(デフォルトは4567)
    -o # ホスト指定(デフォルトは0.0.0.0)
    -e # 環境を指定 (デフォルトはdevelopment)
    -s # rackserver/handlerを指定 (デフォルトはthin)
    -x # mutex lockを付ける (デフォルトはoff)

## 最新開発版について

Sinatraの開発版を使いたい場合は、ローカルに開発版を落として、
`LOAD_PATH`の`sinatra/lib`ディレクトリを指定して実行して下さい。

``` shell
cd myapp
git clone git://github.com/sinatra/sinatra.git
ruby -Isinatra/lib myapp.rb
```

`sinatra/lib`ディレクトリをアプリケーションの`LOAD_PATH`に追加する方法もあります。

``` ruby
$LOAD_PATH.unshift File.dirname(__FILE__) + '/sinatra/lib'
require 'rubygems'
require 'sinatra'

get '/about' do
  "今使ってるバージョンは" + Sinatra::VERSION
end
```

Sinatraのソースを更新する方法:

``` shell
cd myproject/sinatra
git pull
```

## その他

日本語サイト

-   [Greenbear Laboratory
    Rack日本語マニュアル](http://route477.net/w/RackReferenceJa.html)
    - Rackの日本語マニュアル

英語サイト

-   [プロジェクトサイト](http://sinatra.github.com/) - ドキュメント、
    ニュース、他のリソースへのリンクがあります。

-   [プロジェクトに参加(貢献)する](http://sinatra.github.com/contributing.html)
    - バグレポート パッチの送信、サポートなど

-   [Issue tracker](http://github.com/sinatra/sinatra/issues) -
    チケット管理とリリース計画

-   [Twitter](http://twitter.com/sinatra)

-   [メーリングリスト](http://groups.google.com/group/sinatrarb)

-   [IRC: \#sinatra](irc://chat.freenode.net/#sinatra) on
    [freenode.net](http://freenode.net)
