% OSS普及協議会Ruby勉強会3
% ka ([kaosfield](http://www.kaosfield.net))
% 2015-09-11

# はじめに

このスライドは公開されています

このスライドのリポジトリは[https://github.com/kaosf/20150911-oss-slide](https://github.com/kaosf/20150911-oss-slide)にあります

間違いなどを見つけた場合は

<ul>
<li>メールで連絡</li>
<li>Issueで報告</li>
<li>Pull Requestで修正案を提出</li>
</ul>

などしていただけるとありがたいです (※下に行くほど嬉しいです)

# 本日のお題

- gemを作ろう
- RSpecでテスト
- test-unitでテスト
- YARDでドキュメント
- E2Eテスト

# gemを作ろう

# Gitの初期設定

```sh
git config --global user.name yourname
git config --global user.email youremail
```

# gemプロジェクトの初期化

```sh
bundle gem mygem
```

MITライセンスにするとかCode of Conduct宣言を含めるとか聞かれるかも？

とりあえずYESでいいです

# 皆でお互いにgemを使ってみよう

皆にカードを配ります

01 02 ... という数字が書いてます

各々の作るgemの名前が競合しないようにしましょう

```sh
bundle gem tross01
bundle gem tross02
# ...
```

# trossとは

> tross
> 
> 【動詞】 【他動詞】
> 
> 1
> 
> 〈町・コミュニティなどを〉発展させる，〈人を〉育てる.
> 
> 用例
> tross my home twon ふるさとを発展させる.

引用: 英和辞典Woblie

# <span style="color:red">ウソ</span>です

そんな英単語無いです(辞書を引かないように)

<span style="color:red">t</span>okushima.<span style="color:red">r</span>b と とくしま<span style="color:red">OSS</span>普及協議会 の頭文字をとっただけ

# TODOを消そう

以下 `tross00` を作ったと仮定して話を進めます

`tross00.gemspec`内の

```ruby
  spec.summary       = %q{TODO: Write a short summary, because Rubygems requires one.}
  spec.description   = %q{TODO: Write a longer description or delete this line.}
  spec.homepage      = "TODO: Put your gem's website or public repo URL here."
```

を書き換える

```ruby
  spec.summary       = %q{Tross}
  spec.description   = %q{My awesome Tross library}
  spec.homepage      = "http://example.com/tross00"
```

適当でいい

# 次

`tross00.gemspec`内の

```ruby
  # Prevent pushing this gem to RubyGems.org by setting 'allowed_push_host', or
  # delete this section to allow pushing this gem to any host.
  if spec.respond_to?(:metadata)
    spec.metadata['allowed_push_host'] = "TODO: Set to 'http://mygemserver.com'"
  else
    raise "RubyGems 2.0 or newer is required to protect against public gem pushes."
  end
```

を消す

安全のために残しておいても良いけど正しくTODOの部分を書き換えるように

どう書けば正しいのかは後の方で説明します

# 何かを実装する

`lib/tross00.rb`に何か作ってみよう

```ruby
require "tross00/version"

module Tross00
  # Your code goes here...
end
```

# 例

```ruby
require "tross00/version"

module Tross00
  def self.f x
    x + 1
  end
end
```

# gemとしてビルドする

まずはコミット (コミットコメントは適当で良い)

```sh
git add .
git commit -m "Initial commit"
```

次に

```sh
rake build
```

これで `pkg/tross00-0.1.0.gem` が作らる

# gemサーバに上げる

本当はここでRubygems.orgにユーザ登録をして

```sh
rake release
```

をする

こうすれば全世界にgemが公開される

今回はあまり無益過ぎるこんなライブラリを公開してもしょうがないので

geminaboxを使ってプライベートサーバにアップするようにする

# サーバを立ててある

URL: `http://x.y.z.w:9292`

ブラウザでアクセスしてみると良い

※当日に`x.y.z.w`の部分は会場で伝えます

※イベント終了後にサーバは消します

※後からスライドを見てる人は [ここ](http://guides.rubygems.org/run-your-own-gem-server/) の **RUNNING GEM IN A BOX** を参考にして自分でサーバを立てよう

# private gemサーバに上げる

```sh
gem install geminabox
rbenv rehash # rbenvの人限定

gem inabox pkg/tross00-0.1.0.gem
```

サーバのホスト名を聞かれるので

`http://x.y.z.w:9292`

と入力してやる

その設定は `~/.gem/geminabox` に記憶されるのでミスったらここを書き換え

# 作られたgemを使う

`Gemfile`の一行目は通常

```ruby
source 'https://rubygems.org'
```

だが今回は以下のように変更

```ruby
source 'http://x.y.z.w:9292'
```

# Gemfile

Gemfileを作って

```ruby
source 'http://x.y.z.w:9292'

gem 'tross00', '0.1.0'
```

以下を忘れずに

```sh
bundle install
```

# 使ってみる

`a.rb`でも作って

```ruby
require 'tross00'

p Tross00.f 1
```

ターミナルから実行

```sh
ruby a.rb #=> 2
```

# 時間があったら

自分以外の`trossXX`を使ってみよう

他にも好きな名前のgemを作ってアップしてみよう

Rubygems.orgにユーザ登録してみよう

自分で geminabox のサーバを動かしてみよう

# RSpecでテスト

適当なディレクトリを作る

```sh
mkdir sugoiprogram
cd sugoiprogram
```

`lib`ディレクトリと`spec`ディレクトリを作る

```sh
mkdir lib
mkdir spec
```

# ライブラリコードを用意

`lib/a.rb`を用意する

```ruby
def f x, y
  x + y
end

def g x, y
  x * y
end
```

これは凄いプログラムのとんでもなく複雑な実装なのでテストコードを書いて動作を検証せねばならない！

# テストを用意

`spec/a_spec.rb`を用意する

```ruby
require 'rspec'
require './lib/a'

describe "a" do
  describe "f" do
    it "is a sum of x and y" do
      expect(f 1, 2).to eq 3
    end
  end

  describe "g" do
    it "is a product of x and y" do
      expect(g 1, 2).to eq 2
    end
  end
end
```

# RSpecをインストール

```sh
gem install rspec
rbenv rehash # rbenv の人
```

# テスト実行

```sh
rspec spec/a_spec.rb
```

```
..

Finished in 0.0025 seconds (files took 0.24965 seconds to load)
2 examples, 0 failures
```

#

ね？簡単でしょう？

# 色を付ける

`.rspec`というファイルを以下の内容で用意する

```
--color
```

もしくは実行時に`--color`というオプションを渡す

```sh
rspec --color spec/*_spec.rb
```

# rake taskとしてテスト実行出来るようにする

Rakefileを用意する

```ruby
require 'rspec/core/rake_task'

RSpec::Core::RakeTask.new(:spec)

task default: :spec
task test: :spec
```

# rake taskとしてテスト実行出来るようにする

これで

```sh
rake
```

ないし

```sh
rake test
```

でテストが走る

# コードの読み方

```ruby
describe "a" do                 # "a"について
  describe "f" do               # "f"について
    it "is a sum of x and y" do # それはxとyの和なんやで
      expect("実際の値").to eq "期待する値"
                                # "実際の値"は"期待する値"
                                # とequalであると期待されるんやで
    end
  end
end
```

# test-unitでテスト

適当なディレクトリを作る

```sh
mkdir yabaiprogram
cd yabaiprogram
```

`lib`ディレクトリと`test`ディレクトリを作る

```sh
mkdir lib
mkdir test
```

# ライブラリコードを用意

`lib/a.rb`を用意する

```ruby
def f x, y
  x - y
end

def g x, y
  x / y
end
```

これはヤバいプログラムの果てしなく複雑な実装なのでテストコードを書いて動作を検証せねばならない！

# テストを用意

`test/test_a.rb`を用意する

```ruby
require 'test-unit'
require './lib/a'

class TestA < Test::Unit::TestCase
  def test_f
    assert f(2, 1) == 1
  end

  def test_g
    assert g(4, 2) == 2
  end
end
```


# テスト実行

```sh
ruby test/test_a.rb
```

```
Loaded suite test/test_a
Started
..

Finished in 0.001472061 seconds.
-------------------------------------------------------------------------------------------------
2 tests, 2 assertions, 0 failures, 0 errors, 0 pendings, 0 omissions, 0 notifications
100% passed
-------------------------------------------------------------------------------------------------
1358.64 tests/s, 1358.64 assertions/s
```

# rake taskとしてテスト実行出来るようにする

Rakefileを用意する

```ruby
require "rake/testtask"

Rake::TestTask.new do |t|
  t.libs << "test"
  t.test_files = Dir["test/**/test_*.rb"]
  t.verbose = true
end

task default: :test
```

参考: [class Rake::TestTask (Ruby 2.2.0)](http://docs.ruby-lang.org/ja/2.2.0/class/Rake=3a=3aTestTask.html)

# rake taskとしてテスト実行出来るようにする

これでRSpecのときと同様

```sh
rake
```

ないし

```sh
rake test
```

でテストが走る

# power_assertこそパワー

`test/test_a.rb`に以下のテストを追加してみる

```ruby
  def test_power_assert
    assert { g(f(14, 2), 4) == 3.14.to_i + 0.14 }
  end
```

**ブロックの評価結果がtrueになることをassert** …というノリで読めば良い

テストを走らせてみる

```sh
rake
```

# 見よこの美しいエラー表示を

```
Failure:
      assert { g(f(14, 2), 4) == 3.14.to_i + 0.14 }
               | |            |       |    |
               | |            |       |    3.14
               | |            |       3
               | |            false
               | 12
               3
test_power_assert(TestA)
/home/username/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/power_as(略)
test/test_a.rb:14:in `test_power_assert'
     11:   end
     12: 
     13:   def test_power_assert
  => 14:     assert { g(f(14, 2), 4) == 3.14.to_i + 0.14 }
     15:   end
     16: end
```

# YARDでドキュメント作成

適当にプログラムを用意する

とりあえず例によって `a.rb` とする

```
def f x, y
  x - y
end

def g x, y
  raise ZeroDivisionError.new if y == 0
  x / y
end
```

# ドキュメントを書く

```ruby
# Calculate a difference of 2 arguments.
#
# @return [Integer] Difference of x and y
# @param [Integer] x 1st argument
# @param [Integer] y 2nd argument
def f x, y
  x - y
end
```

# ドキュメントを書く

```ruby
# Calculate a quotient of 2 arguments.
#
# @return [Integer] Quotient of x and y
# @param [Integer] x 1st argument
# @param [Integer] y 2nd argument
# @raise [ZeroDivisionError] when y is 0
def g x, y
  raise ZeroDivisionError.new if y == 0
  x / y
end
```

# YARDをインストールする

Gemfileを用意してbundle install実行

```ruby
source 'https://rubygems.org'

gem 'yard'
```

```sh
bundle install
rbenv rehash # rbenv の人
```

# YARDをインストールする

またはgemコマンドで直接インストール

```sh
gem install yard --no-ri --no-rdocument
rbenv rehash # rbenv の人
```

# ドキュメントとなるHTMLを生成

```sh
yard doc a.rb
```

これで `doc/index.html` やその他HTMLが生成される

# To be continued...
