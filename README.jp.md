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

## 目次

* [Sinatra](#sinatra)
    * [目次](#目次)
    * [ルーティング(Routes)](#ルーティングroutes)
    * [条件(Conditions)](#条件conditions)
    * [戻り値(Return Values)](#戻り値return-values)
    * [カスタムルーティングマッチャー(Custom Route Matchers)](#カスタムルーティングマッチャーcustom-route-matchers)
    * [静的ファイル(Static Files)](#静的ファイルstatic-files)
    * [ビュー / テンプレート(Views / Templates)](#ビュー--テンプレートviews--templates)
        * [リテラルテンプレート(Literal Templates)](#リテラルテンプレートliteral-templates)
        * [利用可能なテンプレート言語](#利用可能なテンプレート言語)
            * [Haml テンプレート](#haml-テンプレート)
            * [Erb テンプレート](#erb-テンプレート)
            * [Builder テンプレート](#builder-テンプレート)
            * [Nokogiri テンプレート](#nokogiri-テンプレート)
            * [Sass テンプレート](#sass-テンプレート)
            * [SCSS テンプレート](#scss-テンプレート)
            * [Less テンプレート](#less-テンプレート)
            * [Liquid テンプレート](#liquid-テンプレート)
            * [Markdown テンプレート](#markdown-テンプレート)
            * [Textile テンプレート](#textile-テンプレート)
            * [RDoc テンプレート](#rdoc-テンプレート)
            * [Radius テンプレート](#radius-テンプレート)
            * [Markaby テンプレート](#markaby-テンプレート)
            * [RABL テンプレート](#rabl-テンプレート)
            * [Slim テンプレート](#slim-テンプレート)
            * [Creole テンプレート](#creole-テンプレート)
            * [CoffeeScript テンプレート](#coffeescript-テンプレート)
            * [Stylus テンプレート](#stylus-テンプレート)
            * [Yajl テンプレート](#yajl-テンプレート)
            * [WLang テンプレート](#wlang-テンプレート)
        * [テンプレート内での変数へのアクセス](#テンプレート内での変数へのアクセス)
        * [`yield`を伴うテンプレートとネストしたレイアウト](#yieldを伴うテンプレートとネストしたレイアウト)
        * [インラインテンプレート(Inline Templates)](#インラインテンプレートinline-templates)
        * [名前付きテンプレート(Named Templates)](#名前付きテンプレートnamed-templates)
        * [ファイル拡張子の関連付け](#ファイル拡張子の関連付け)
        * [オリジナルテンプレートエンジンの追加](#オリジナルテンプレートエンジンの追加)
    * [フィルタ(Filters)](#フィルタfilters)
    * [ヘルパー(Helpers)](#ヘルパーhelpers)
        * [セッションの使用](#セッションの使用)
        * [停止(Halting)](#停止halting)
        * [パッシング(Passing)](#パッシングpassing)
        * [別ルーティングの誘発](#別ルーティングの誘発)
        * [ボディ、ステータスコードおよびヘッダの設定](#ボディステータスコードおよびヘッダの設定)
        * [ストリーミングレスポンス(Streaming Responses)](#ストリーミングレスポンスstreaming-responses)
        * [ロギング(Logging)](#ロギングlogging)
        * [MIMEタイプ(Mime Types)](#mimeタイプmime-types)
        * [URLの生成](#urlの生成)
        * [ブラウザリダイレクト(Browser Redirect)](#ブラウザリダイレクトbrowser-redirect)
        * [キャッシュ制御(Cache Control)](#キャッシュ制御cache-control)
        * [ファイルの送信(Sending Files)](#ファイルの送信sending-files)
        * [リクエストオブジェクトへのアクセス(Accessing the Request Object)](#リクエストオブジェクトへのアクセスaccessing-the-request-object)
        * [アタッチメント(Attachments)](#アタッチメントattachments)
        * [日付と時刻の取り扱い(Dealing with Date and Time)](#日付と時刻の取り扱いdealing-with-date-and-time)
        * [テンプレートファイルの探索(Looking Up Template Files)](#テンプレートファイルの探索looking-up-template-files)
    * [コンフィギュレーション(Configuration)](#コンフィギュレーションconfiguration)
        * [攻撃防御に対する設定(Configuring attack protection)](#攻撃防御に対する設定configuring-attack-protection)
        * [利用可能な設定(Available Settings)](#利用可能な設定available-settings)
    * [環境設定(Environments)](#環境設定environments)
    * [エラーハンドリング(Error Handling)](#エラーハンドリングerror-handling)
        * [Not Found](#not-found)
        * [エラー(Error)](#エラーerror)
    * [Rackミドルウェア(Rack Middleware)](#rackミドルウェアrack-middleware)
    * [テスト(Testing)](#テストtesting)
    * [Sinatra::Base - ミドルウェア、ライブラリおよびモジュラーアプリ](#sinatrabase---ミドルウェアライブラリおよびモジュラーアプリ)
        * [モジュラースタイル vs クラッシックスタイル(Modular vs. Classic Style)](#モジュラースタイル-vs-クラッシックスタイルmodular-vs-classic-style)
        * [モジュラーアプリケーションの提供(Serving a Modular Application)](#モジュラーアプリケーションの提供serving-a-modular-application)
        * [config.ruを用いたクラッシックスタイルアプリケーションの使用](#configruを用いたクラッシックスタイルアプリケーションの使用)
        * [config.ruはいつ使うのか？](#configruはいつ使うのか)
        * [Sinatraのミドルウェアとしての利用(Using Sinatra as Middleware)](#sinatraのミドルウェアとしての利用using-sinatra-as-middleware)
        * [動的なアプリケーションの生成(Dynamic Application Creation)](#動的なアプリケーションの生成dynamic-application-creation)
    * [スコープとバインディング(Scopes and Binding)](#スコープとバインディングscopes-and-binding)
        * [アプリケーション/クラスのスコープ(Application/Class Scope)](#アプリケーションクラスのスコープapplicationclass-scope)
        * [リクエスト/インスタンスのスコープ(Request/Instance Scope)](#リクエストインスタンスのスコープrequestinstance-scope)
        * [デリゲートスコープ(Delegation Scope)](#デリゲートスコープdelegation-scope)
    * [コマンドライン(Command Line)](#コマンドラインcommand-line)
    * [必要環境(Requirement)](#必要環境requirement)
    * [最新開発版(The Bleeding Edge)](#最新開発版the-bleeding-edge)
        * [Bundlerを使う場合](#bundlerを使う場合)
        * [直接組み込む場合(Roll Your Own)](#直接組み込む場合roll-your-own)
        * [グローバル環境にインストールする場合(Install Globally)](#グローバル環境にインストールする場合install-globally)
    * [バージョニング(Versioning)](#バージョニングversioning)
    * [参考文献(Further Reading)](#参考文献further-reading)

## ルーティング(Routes)

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

### 条件(Conditions)

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

### 戻り値(Return Values)

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

## カスタムルーティングマッチャー(Custom Route Matchers)

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


## 静的ファイル(Static Files)

静的ファイルは`./public`ディレクトリから配信されます。
`:public_folder`オプションを指定することで別の場所を指定することができます。

``` ruby
set :public_folder, File.dirname(__FILE__) + '/static'
```

ノート: この静的ファイル用のディレクトリ名はURL中に含まれません。
例えば、`./public/css/style.css`は`http://example.com/css/style.css`でアクセスできます。

## ビュー / テンプレート(Views / Templates)

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

### リテラルテンプレート(Literal Templates)

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

### テンプレート内での変数へのアクセス

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


### インラインテンプレート(Inline Templates)

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

### 名前付きテンプレート(Named Templates)

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

## フィルタ(Filters)

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

## ヘルパー(Helpers)

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

### 停止(Halting)

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

### 別ルーティングの誘発

Sometimes `pass` is not what you want, instead you would like to get the result
of calling another route. Simply use `call` to achieve this:

``` ruby
get '/foo' do
  status, headers, body = call env.merge("PATH_INFO" => '/bar')
  [status, headers, body.map(&:upcase)]
end

get '/bar' do
  "bar"
end
```

Note that in the example above, you would ease testing and increase performance
by simply moving `"bar"` into a helper used by both `/foo`
and `/bar`.

If you want the request to be sent to the same application instance rather than
a duplicate, use `call!` instead of `call`.

Check out the Rack specification if you want to learn more about `call`.

### ボディ、ステータスコードおよびヘッダの設定

It is possible and recommended to set the status code and response body with the
return value of the route block. However, in some scenarios you might want to
set the body at an arbitrary point in the execution flow. You can do so with the
`body` helper method. If you do so, you can use that method from there on to
access the body:

``` ruby
get '/foo' do
  body "bar"
end

after do
  puts body
end
```

It is also possible to pass a block to `body`, which will be executed by the
Rack handler (this can be used to implement streaming, see "Return Values").

Similar to the body, you can also set the status code and headers:

``` ruby
get '/foo' do
  status 418
  headers \
    "Allow"   => "BREW, POST, GET, PROPFIND, WHEN",
    "Refresh" => "Refresh: 20; http://www.ietf.org/rfc/rfc2324.txt"
  body "I'm a tea pot!"
end
```

Like `body`, `headers` and `status` with no arguments can be used to access
their current values.

### ストリーミングレスポンス(Streaming Responses)

Sometimes you want to start sending out data while still generating parts of
the response body. In extreme examples, you want to keep sending data until
the client closes the connection. You can use the `stream` helper to avoid
creating your own wrapper:

``` ruby
get '/' do
  stream do |out|
    out << "It's gonna be legen -\n"
    sleep 0.5
    out << " (wait for it) \n"
    sleep 1
    out << "- dary!\n"
  end
end
```

This allows you to implement streaming APIs,
[Server Sent Events](http://dev.w3.org/html5/eventsource/), and can be used as
the basis for [WebSockets](http://en.wikipedia.org/wiki/WebSocket). It can also be
used to increase throughput if some but not all content depends on a slow
resource.

Note that the streaming behavior, especially the number of concurrent requests,
highly depends on the web server used to serve the application. Some servers,
like WEBRick, might not even support streaming at all. If the server does not
support streaming, the body will be sent all at once after the block passed to
`stream` finishes executing. Streaming does not work at all with Shotgun.

If the optional parameter is set to `keep_open`, it will not call `close` on
the stream object, allowing you to close it at any later point in the
execution flow. This only works on evented servers, like Thin and Rainbows.
Other servers will still close the stream:

``` ruby
# long polling

set :server, :thin
connections = []

get '/subscribe' do
  # register a client's interest in server events
  stream(:keep_open) { |out| connections << out }

  # purge dead connections
  connections.reject!(&:closed?)

  # acknowledge
  "subscribed"
end

post '/message' do
  connections.each do |out|
    # notify client that a new message has arrived
    out << params[:message] << "\n"

    # indicate client to connect again
    out.close
  end

  # acknowledge
  "message received"
end
```

### ロギング(Logging)

In the request scope, the `logger` helper exposes a `Logger` instance:

``` ruby
get '/' do
  logger.info "loading data"
  # ...
end
```

This logger will automatically take your Rack handler's logging settings into
account. If logging is disabled, this method will return a dummy object, so
you do not have to worry about it in your routes and filters.

Note that logging is only enabled for `Sinatra::Application` by
default, so if you inherit from `Sinatra::Base`, you probably want to
enable it yourself:

``` ruby
class MyApp < Sinatra::Base
  configure :production, :development do
    enable :logging
  end
end
```

To avoid any logging middleware to be set up, set the `logging` setting to
`nil`. However, keep in mind that `logger` will in that case return `nil`. A
common use case is when you want to set your own logger. Sinatra will use
whatever it will find in `env['rack.logger']`.


### MIMEタイプ(Mime Types)

`send_file`か静的ファイルを使う時、SinatraがMIMEタイプを理解できない場合があります。その時は `mime_type` を使ってファイル拡張子毎に登録して下さい。

``` ruby
mime_type :foo, 'text/foo'
```

これは`content_type`ヘルパーで利用することができます:

``` ruby
get '/' do
  content_type :foo
  "foo foo foo"
end
```

### URLの生成

For generating URLs you should use the `url` helper method, for instance, in
Haml:

``` ruby
%a{:href => url('/foo')} foo
```

It takes reverse proxies and Rack routers into account, if present.

This method is also aliased to `to` (see below for an example).

### ブラウザリダイレクト(Browser Redirect)

You can trigger a browser redirect with the `redirect` helper method:

``` ruby
get '/foo' do
  redirect to('/bar')
end
```

Any additional parameters are handled like arguments passed to `halt`:

``` ruby
redirect to('/bar'), 303
redirect 'http://google.com', 'wrong place, buddy'
```

You can also easily redirect back to the page the user came from with
`redirect back`:

``` ruby
get '/foo' do
  "<a href='/bar'>do something</a>"
end

get '/bar' do
  do_something
  redirect back
end
```

To pass arguments with a redirect, either add them to the query:

``` ruby
redirect to('/bar?sum=42')
```

Or use a session:

``` ruby
enable :sessions

get '/foo' do
  session[:secret] = 'foo'
  redirect to('/bar')
end

get '/bar' do
  session[:secret]
end
```

### キャッシュ制御(Cache Control)

Setting your headers correctly is the foundation for proper HTTP caching.

You can easily set the Cache-Control header like this:

``` ruby
get '/' do
  cache_control :public
  "cache it!"
end
```

Pro tip: Set up caching in a before filter:

``` ruby
before do
  cache_control :public, :must_revalidate, :max_age => 60
end
```

If you are using the `expires` helper to set the corresponding header,
`Cache-Control` will be set automatically for you:

``` ruby
before do
  expires 500, :public, :must_revalidate
end
```

To properly use caches, you should consider using `etag` or `last_modified`.
It is recommended to call those helpers *before* doing any heavy lifting, as they
will immediately flush a response if the client already has the current
version in its cache:

``` ruby
get '/article/:id' do
  @article = Article.find params[:id]
  last_modified @article.updated_at
  etag @article.sha1
  erb :article
end
```

It is also possible to use a
[weak ETag](http://en.wikipedia.org/wiki/HTTP_ETag#Strong_and_weak_validation):

``` ruby
etag @article.sha1, :weak
```

These helpers will not do any caching for you, but rather feed the necessary
information to your cache. If you are looking for a quick reverse-proxy caching
solution, try [rack-cache](https://github.com/rtomayko/rack-cache):

``` ruby
require "rack/cache"
require "sinatra"

use Rack::Cache

get '/' do
  cache_control :public, :max_age => 36000
  sleep 5
  "hello"
end
```

Use the `:static_cache_control` setting (see below) to add
`Cache-Control` header info to static files.

According to RFC 2616, your application should behave differently if the If-Match
or If-None-Match header is set to `*`, depending on whether the resource
requested is already in existence. Sinatra assumes resources for safe (like get)
and idempotent (like put) requests are already in existence, whereas other
resources (for instance post requests) are treated as new resources. You
can change this behavior by passing in a `:new_resource` option:

``` ruby
get '/create' do
  etag '', :new_resource => true
  Article.create
  erb :new_article
end
```

If you still want to use a weak ETag, pass in a `:kind` option:

``` ruby
etag '', :new_resource => true, :kind => :weak
```

### ファイルの送信(Sending Files)

For sending files, you can use the `send_file` helper method:

``` ruby
get '/' do
  send_file 'foo.png'
end
```

It also takes options:

``` ruby
send_file 'foo.png', :type => :jpg
```

The options are:

<dl>
  <dt>filename</dt>
    <dd>file name, in response, defaults to the real file name.</dd>

  <dt>last_modified</dt>
    <dd>value for Last-Modified header, defaults to the file's mtime.</dd>

  <dt>type</dt>
    <dd>content type to use, guessed from the file extension if missing.</dd>

  </dt>disposition</dt>
    <dd>
      used for Content-Disposition, possible value: <tt>nil</tt> (default),
      <tt>:attachment</tt> and <tt>:inline</tt>
    </dd>

  <dt>length</dt>
    <dd>Content-Length header, defaults to file size.</dd>

  <dt>status</dt>
    <dd>
      Status code to be send. Useful when sending a static file as an error page.

      If supported by the Rack handler, other means than streaming from the Ruby
      process will be used. If you use this helper method, Sinatra will automatically
      handle range requests.
    </dd>
</dl>


## リクエストオブジェクトへのアクセス(Accessing the Request Object)

受信するリクエストオブジェクトは、`request`メソッドを通じてリクエストレベル(フィルタ、ルーティング、エラーハンドラ)からアクセスすることができます:

``` ruby
# アプリケーションが http://example.com/example で動作している場合
get '/foo' do
  t = %w[text/css text/html application/javascript]
  request.accept              # ['text/html', '*/*']
  request.accept? 'text/xml'  # true
  request.preferred_type(t)   # 'text/html'
  request.body                # クライアントによって送信されたリクエストボディ(下記参照)
  request.scheme              # "http"
  request.script_name         # "/example"
  request.path_info           # "/foo"
  request.port                # 80
  request.request_method      # "GET"
  request.query_string        # ""
  request.content_length      # request.bodyの長さ
  request.media_type          # request.bodyのメディアタイプ
  request.host                # "example.com"
  request.get?                # true (他の動詞にも同種メソッドあり)
  request.form_data?          # false
  request["some_param"]       # some_param変数の値。[]はパラメータハッシュのショートカット
  request.referrer            # クライアントのリファラまたは'/'
  request.user_agent          # ユーザエージェント (:agent 条件によって使用される)
  request.cookies             # ブラウザクッキーのハッシュ
  request.xhr?                # Ajaxリクエストかどうか
  request.url                 # "http://example.com/example/foo"
  request.path                # "/example/foo"
  request.ip                  # クライアントのIPアドレス
  request.secure?             # false (sslではtrueになる)
  request.forwarded?          # true (リバースプロキシの裏で動いている場合)
  request.env                 # Rackによって渡された生のenvハッシュ
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

### アタッチメント(Attachments)

You can use the `attachment` helper to tell the browser the response should be
stored on disk rather than displayed in the browser:

``` ruby
get '/' do
  attachment
  "store it!"
end
```

You can also pass it a file name:

``` ruby
get '/' do
  attachment "info.txt"
  "store it!"
end
```

### 日付と時刻の取り扱い(Dealing with Date and Time)

Sinatra offers a `time_for` helper method that generates a Time object
from the given value. It is also able to convert `DateTime`, `Date` and
similar classes:

``` ruby
get '/' do
  pass if Time.now > time_for('Dec 23, 2012')
  "still time"
end
```

This method is used internally by `expires`, `last_modified` and akin. You can
therefore easily extend the behavior of those methods by overriding `time_for`
in your application:

``` ruby
helpers do
  def time_for(value)
    case value
    when :yesterday then Time.now - 24*60*60
    when :tomorrow  then Time.now + 24*60*60
    else super
    end
  end
end

get '/' do
  last_modified :yesterday
  expires :tomorrow
  "hello"
end
```

### テンプレートファイルの探索(Looking Up Template Files)

The `find_template` helper is used to find template files for rendering:

``` ruby
find_template settings.views, 'foo', Tilt[:haml] do |file|
  puts "could be #{file}"
end
```

This is not really useful. But it is useful that you can actually override this
method to hook in your own lookup mechanism. For instance, if you want to be
able to use more than one view directory:

``` ruby
set :views, ['views', 'templates']

helpers do
  def find_template(views, name, engine, &block)
    Array(views).each { |v| super(v, name, engine, &block) }
  end
end
```

Another example would be using different directories for different engines:

``` ruby
set :views, :sass => 'views/sass', :haml => 'templates', :default => 'views'

helpers do
  def find_template(views, name, engine, &block)
    _, folder = views.detect { |k,v| engine == Tilt[k] }
    folder ||= views[:default]
    super(folder, name, engine, &block)
  end
end
```

You can also easily wrap this up in an extension and share with others!

Note that `find_template` does not check if the file really exists but
rather calls the given block for all possible paths. This is not a performance
issue, since `render` will use `break` as soon as a file is found. Also,
template locations (and content) will be cached if you are not running in
development mode. You should keep that in mind if you write a really crazy
method.

## コンフィギュレーション(Configuration)

どの環境でも起動時に１回だけ実行されます。

``` ruby
configure do
  # １つのオプションをセット
  set :option, 'value'

  # 複数のオプションをセット
  set :a => 1, :b => 2

  # `set :option, true`と同じ
  enable :option

  # `set :option, false`と同じ
  disable :option

  # ブロックを使って動的な設定をすることもできます。
  set(:css_dir) { File.join(views, 'css') }
end
```


環境設定(`RACK_ENV`環境変数)が`:production`に設定されている時だけ実行する方法:

``` ruby
configure :production do
  ...
end
```

環境設定が`:production`か`:test`に設定されている時だけ実行する方法:

``` ruby
configure :production, :test do
  ...
end
```

設定したオプションには`settings`からアクセスできます:

``` ruby
configure do
  set :foo, 'bar'
end

get '/' do
  settings.foo? # => true
  settings.foo  # => 'bar'
  ...
end
```

### 攻撃防御に対する設定(Configuring attack protection)

Sinatra is using
[Rack::Protection](https://github.com/rkh/rack-protection#readme) to defend
your application against common, opportunistic attacks. You can easily disable
this behavior (which will open up your application to tons of common
vulnerabilities):

``` ruby
disable :protection
```

To skip a single defense layer, set `protection` to an options hash:

``` ruby
set :protection, :except => :path_traversal
```
You can also hand in an array in order to disable a list of protections:

``` ruby
set :protection, :except => [:path_traversal, :session_hijacking]
```

By default, Sinatra will only set up session based protection if `:sessions`
has been enabled. Sometimes you want to set up sessions on your own, though. In
that case you can get it to set up session based protections by passing the
`:session` option:

``` ruby
use Rack::Session::Pool
set :protection, :session => true
```

### 利用可能な設定(Available Settings)

<dl>
  <dt>absolute_redirects</dt>
  <dd>
    If disabled, Sinatra will allow relative redirects, however, Sinatra will no
    longer conform with RFC 2616 (HTTP 1.1), which only allows absolute redirects.
  </dd>
  <dd>
    Enable if your app is running behind a reverse proxy that has not been set up
    properly. Note that the <tt>url</tt> helper will still produce absolute URLs, unless you
    pass in <tt>false</tt> as the second parameter.
  </dd>
  <dd>Disabled by default.</dd>

  <dt>add_charsets</dt>
  <dd>
    Mime types the <tt>content_type</tt> helper will automatically add the charset info to.
    You should add to it rather than overriding this option:
    <tt>settings.add_charsets << "application/foobar"</tt>
  </dd>

  <dt>app_file</dt>
  <dd>
    Path to the main application file, used to detect project root, views and public
    folder and inline templates.
  </dd>

  <dt>bind</dt>
  <dd>IP address to bind to (default: <tt>0.0.0.0</tt> <em>or</em> <tt>localhost</tt> if your `environment` is set to development.). Only used for built-in server.</dd>

  <dt>default_encoding</dt>
  <dd>Encoding to assume if unknown (defaults to <tt>"utf-8"</tt>).</dd>

  <dt>dump_errors</dt>
  <dd>Display errors in the log.</dd>

  <dt>environment</dt>
  <dd>
    Current environment. Defaults to <tt>ENV['RACK_ENV']</tt>, or <tt>"development"</tt> if
    not available.
  </dd>

  <dt>logging</dt>
  <dd>Use the logger.</dd>

  <dt>lock</dt>
  <dd>
    Places a lock around every request, only running processing on request
    per Ruby process concurrently.
  </dd>
  <dd>Enabled if your app is not thread-safe. Disabled per default.</dd>

  <dt>method_override</dt>
  <dd>
    Use <tt>_method</tt> magic to allow put/delete forms in browsers that
    don't support it.
  </dd>

  <dt>port</dt>
  <dd>Port to listen on. Only used for built-in server.</dd>

  <dt>prefixed_redirects</dt>
  <dd>
    Whether or not to insert <tt>request.script_name</tt> into redirects if no
    absolute path is given. That way <tt>redirect '/foo'</tt> would behave like
    <tt>redirect to('/foo')</tt>. Disabled per default.
  </dd>

  <dt>protection</dt>
  <dd>Whether or not to enable web attack protections. See protection section above.</dd>

  <dt>public_dir</dt>
  <dd>Alias for <tt>public_folder</tt>. See below.</dd>

  <dt>public_folder</dt>
  <dd>
    Path to the folder public files are served from. Only used if static
    file serving is enabled (see <tt>static</tt> setting below). Inferred from
    <tt>app_file</tt> setting if not set.
  </dd>

  <dt>reload_templates</dt>
  <dd>
    Whether or not to reload templates between requests. Enabled in development mode.
  </dd>

  <dt>root</dt>
  <dd>
    Path to project root folder. Inferred from <tt>app_file</tt> setting if not set.
  </dd>

  <dt>raise_errors</dt>
  <dd>
    Raise exceptions (will stop application). Enabled by default when
    <tt>environment</tt> is set to <tt>"test"</tt>, disabled otherwise.
  </dd>

  <dt>run</dt>
  <dd>
    If enabled, Sinatra will handle starting the web server. Do not
    enable if using rackup or other means.
  </dd>

  <dt>running</dt>
  <dd>Is the built-in server running now? Do not change this setting!</dd>

  <dt>server</dt>
  <dd>
    Server or list of servers to use for built-in server. Order indicates
    priority, default depends on Ruby implementation.
  </dd>

  <dt>sessions</dt>
  <dd>
    Enable cookie-based sessions support using <tt>Rack::Session::Cookie</tt>.
    See 'Using Sessions' section for more information.
  </dd>

  <dt>show_exceptions</dt>
  <dd>
    Show a stack trace in the browser when an exception
    happens. Enabled by default when <tt>environment</tt>
    is set to <tt>"development"</tt>, disabled otherwise.
  </dd>
  <dd>
    Can also be set to <tt>:after_handler</tt> to trigger
    app-specified error handling before showing a stack
    trace in the browser.
  </dd>

  <dt>static</dt>
  <dd>Whether Sinatra should handle serving static files.</dd>
  <dd>Disable when using a server able to do this on its own.</dd>
  <dd>Disabling will boost performance.</dd>
  <dd>
    Enabled per default in classic style, disabled for
    modular apps.
  </dd>

  <dt>static_cache_control</dt>
  <dd>
    When Sinatra is serving static files, set this to add
    <tt>Cache-Control</tt> headers to the responses. Uses the
    <tt>cache_control</tt> helper. Disabled by default.
  </dd>
  <dd>
    Use an explicit array when setting multiple values:
    <tt>set :static_cache_control, [:public, :max_age => 300]</tt>
  </dd>

  <dt>threaded</dt>
  <dd>
    If set to <tt>true</tt>, will tell Thin to use <tt>EventMachine.defer</tt>
    for processing the request.
  </dd>

  <dt>views</dt>
  <dd>
    Path to the views folder. Inferred from <tt>app_file</tt> setting if
    not set.
  </dd>

  <dt>x_cascade</dt>
  <dd>
    Whether or not to set the X-Cascade header if no route matches.
    Defaults to <tt>true</tt>.
  </dd>
</dl>

## 環境設定(Environments)

There are three predefined `environments`: `"development"`,
`"production"` and `"test"`. Environments can be set
through the `RACK_ENV` environment variable. The default value is
`"development"`. In the `"development"` environment all templates are reloaded between
requests, and special `not_found` and `error` handlers
display stack traces in your browser.
In the `"production"` and `"test"` environments, templates are cached by default.

To run different environments, set the `RACK_ENV` environment variable:

``` shell
RACK_ENV=production ruby my_app.rb
```

You can use predefined methods: `development?`, `test?` and `production?` to
check the current environment setting:

``` ruby
get '/' do
  if settings.development?
    "development!"
  else
    "not development!"
  end
end
```

## エラーハンドリング(Error Handling)

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

### エラー(Error)

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


## Rackミドルウェア(Rack Middleware)

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

## テスト(Testing)

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

## Sinatra::Base - ミドルウェア、ライブラリおよびモジュラーアプリ

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

### モジュラースタイル vs クラッシックスタイル(Modular vs. Classic Style)

Contrary to common belief, there is nothing wrong with the classic style. If it
suits your application, you do not have to switch to a modular application.

The main disadvantage of using the classic style rather than the modular style is that
you will only have one Sinatra application per Ruby process. If you plan to use
more than one, switch to the modular style. There is no reason you cannot mix
the modular and the classic styles.

If switching from one style to the other, you should be aware of slightly
different default settings:

<table>
  <tr>
    <th>Setting</th>
    <th>Classic</th>
    <th>Modular</th>
  </tr>

  <tr>
    <td>app_file</td>
    <td>file loading sinatra</td>
    <td>file subclassing Sinatra::Base</td>
  </tr>

  <tr>
    <td>run</td>
    <td>$0 == app_file</td>
    <td>false</td>
  </tr>

  <tr>
    <td>logging</td>
    <td>true</td>
    <td>false</td>
  </tr>

  <tr>
    <td>method_override</td>
    <td>true</td>
    <td>false</td>
  </tr>

  <tr>
    <td>inline_templates</td>
    <td>true</td>
    <td>false</td>
  </tr>

  <tr>
    <td>static</td>
    <td>true</td>
    <td>false</td>
  </tr>
</table>

### モジュラーアプリケーションの提供(Serving a Modular Application)

There are two common options for starting a modular app, actively starting with
`run!`:

``` ruby
# my_app.rb
require 'sinatra/base'

class MyApp < Sinatra::Base
  # ... app code here ...

  # start the server if ruby file executed directly
  run! if app_file == $0
end
```

Start with:

``` shell
ruby my_app.rb
```

Or with a `config.ru` file, which allows using any Rack handler:

``` ruby
# config.ru (run with rackup)
require './my_app'
run MyApp
```

Run:

``` shell
rackup -p 4567
```

### config.ruを用いたクラッシックスタイルアプリケーションの使用

Write your app file:

``` ruby
# app.rb
require 'sinatra'

get '/' do
  'Hello world!'
end
```

And a corresponding `config.ru`:

``` ruby
require './app'
run Sinatra::Application
```

### config.ruはいつ使うのか？

A `config.ru` file is recommended if:

* You want to deploy with a different Rack handler (Passenger, Unicorn,
  Heroku, ...).
* You want to use more than one subclass of `Sinatra::Base`.
* You want to use Sinatra only for middleware, and not as an endpoint.

**There is no need to switch to a `config.ru` simply because you
switched to the modular style, and you don't have to use the modular style for running
with a `config.ru`.**

### Sinatraのミドルウェアとしての利用(Using Sinatra as Middleware)

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

### 動的なアプリケーションの生成(Dynamic Application Creation)

Sometimes you want to create new applications at runtime without having to
assign them to a constant. You can do this with `Sinatra.new`:

``` ruby
require 'sinatra/base'
my_app = Sinatra.new { get('/') { "hi" } }
my_app.run!
```

It takes the application to inherit from as an optional argument:

```ruby
# config.ru (run with rackup)
require 'sinatra/base'

controller = Sinatra.new do
  enable :logging
  helpers MyHelpers
end

map('/a') do
  run Sinatra.new(controller) { get('/') { 'a' } }
end

map('/b') do
  run Sinatra.new(controller) { get('/') { 'b' } }
end
```

This is especially useful for testing Sinatra extensions or using Sinatra in
your own library.

This also makes using Sinatra as middleware extremely easy:

``` ruby
require 'sinatra/base'

use Sinatra do
  get('/') { ... }
end

run RailsProject::Application
```

## スコープとバインディング(Scopes and Binding)

現在のスコープはどのメソッドや変数が利用可能かを決定します。

### アプリケーション/クラスのスコープ(Application/Class Scope)

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

### リクエスト/インスタンスのスコープ(Request/Instance Scope)

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

### デリゲートスコープ(Delegation Scope)

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

## コマンドライン(Command Line)

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

## 必要環境(Requirement)

The following Ruby versions are officially supported:
<dl>
  <dt>Ruby 1.8.7</dt>
  <dd>
    1.8.7 is fully supported, however, if nothing is keeping you from it, we
    recommend upgrading or switching to JRuby or Rubinius. Support for 1.8.7
    will not be dropped before Sinatra 2.0. Ruby 1.8.6 is no longer supported.
  </dd>

  <dt>Ruby 1.9.2</dt>
  <dd>
    1.9.2 is fully supported. Do not use 1.9.2p0, as it is known to cause
    segmentation faults when running Sinatra. Official support will continue
    at least until the release of Sinatra 1.5.
  </dd>

  <dt>Ruby 1.9.3</dt>
  <dd>
    1.9.3 is fully supported and recommended. Please note that switching to 1.9.3
    from an earlier version will invalidate all sessions. 1.9.3 will be supported
    until the release of Sinatra 2.0.
  </dd>

  <dt>Ruby 2.0.0</dt>
  <dd>
    2.0.0 is fully supported and recommended. There are currently no plans to drop
    official support for it.
  </dd>

  <dt>Rubinius</dt>
  <dd>
    Rubinius is officially supported (Rubinius >= 2.x). It is recommended to
    <tt>gem install puma</tt>.
  </dd>

  <dt>JRuby</dt>
  <dd>
    The latest stable release of JRuby is officially supported. It is not
    recommended to use C extensions with JRuby. It is recommended to
    <tt>gem install trinidad</tt>.
  </dd>
</dl>

We also keep an eye on upcoming Ruby versions.

The following Ruby implementations are not officially supported but still are
known to run Sinatra:

* Older versions of JRuby and Rubinius
* Ruby Enterprise Edition
* MacRuby, Maglev, IronRuby
* Ruby 1.9.0 and 1.9.1 (but we do recommend against using those)

Not being officially supported means if things only break there and not on a
supported platform, we assume it's not our issue but theirs.

We also run our CI against ruby-head (the upcoming 2.1.0), but we can't
guarantee anything, since it is constantly moving. Expect 2.1.0 to be fully
supported.

Sinatra should work on any operating system supported by the chosen Ruby
implementation.

If you run MacRuby, you should `gem install control_tower`.

Sinatra currently doesn't run on Cardinal, SmallRuby, BlueRuby or any
Ruby version prior to 1.8.7.

## 最新開発版(The Bleeding Edge)

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

### Bundlerを使う場合

If you want to run your application with the latest Sinatra, using
[Bundler](http://gembundler.com/) is the recommended way.

First, install bundler, if you haven't:

``` shell
gem install bundler
```

Then, in your project directory, create a `Gemfile`:

```ruby
source 'https://rubygems.org'
gem 'sinatra', :github => "sinatra/sinatra"

# other dependencies
gem 'haml'                    # for instance, if you use haml
gem 'activerecord', '~> 3.0'  # maybe you also need ActiveRecord 3.x
```

Note that you will have to list all your application's dependencies in the `Gemfile`.
Sinatra's direct dependencies (Rack and Tilt) will, however, be automatically
fetched and added by Bundler.

Now you can run your app like this:

``` shell
bundle exec ruby myapp.rb
```

### 直接組み込む場合(Roll Your Own)

Create a local clone and run your app with the `sinatra/lib` directory
on the `$LOAD_PATH`:

``` shell
cd myapp
git clone git://github.com/sinatra/sinatra.git
ruby -I sinatra/lib myapp.rb
```

To update the Sinatra sources in the future:

``` shell
cd myapp/sinatra
git pull
```

### グローバル環境にインストールする場合(Install Globally)

You can build the gem on your own:

``` shell
git clone git://github.com/sinatra/sinatra.git
cd sinatra
rake sinatra.gemspec
rake install
```

If you install gems as root, the last step should be:

``` shell
sudo rake install
```

## バージョニング(Versioning)

Sinatra follows [Semantic Versioning](http://semver.org/), both SemVer and
SemVerTag.

## 参考文献(Further Reading)

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
