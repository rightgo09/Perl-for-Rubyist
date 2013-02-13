Perl-for-Rubyist
================

RubyistのためのPerl

**現在順不同**

とりあえずPerlを書く時は
--------

- strict/warningsプラグマを各ファイルの先頭に書くべし

```perl
use strict;
use warnings;
```

変数
--------

### シジル
- Perlには変数にシジル(記号)をつける必要があり、スカラ変数・配列・ハッシュでそれぞれ異なる

```ruby
# ruby
num = 123
array = [1, 2, 3]
h = { one: 1, two: 2, three: 3 }

array[2]  #=> 3
h[:three] #=> 3
```
```perl
# perl
my $num = 123;
my @array = (1, 2, 3);
my %h = ( one => 1, two => 2, three => 3);

$array[2]; #=> 3
$h{three}; #=> 3
```

### スコープ
- 変数スコープはmyをつけると{から}までになる。トップレベルならファイルの最終行まで
- Rubyと異なり、外側へと探索が行われる

```ruby
# ruby
hoge = 123
def foo
  hoge
end
foo()
#=> NameError: undefined local variable or method `hoge' for main:Object
```
```perl
# perl
my $hoge = 123;
sub foo {
  $hoge;
}
foo(); #=> 123
```

オブジェクト
--------

- Rubyのように、「数値を書いたら」「文字列を置けば」それがオブジェクト **というわけではない** 。 **全くない**
- **もっと頑張る必要がある**

### クラス

- Perlのクラス名は「package」で宣言する
- そのままnamespaceの意味である

```ruby
# ruby
class Hoge
end
```
```perl
# perl
package Hoge {
}
```

### コンストラクタ

- Perlにはない
- なので別に好きな名前で作れるけれど、通常は「new」

```ruby
# ruby
class Hoge
end
Hoge.new
```
```perl
# perl
package Hoge {
  sub new {
  }
}
Hoge->new # 動くけどまだ無意味
```

### リファレンス

- Perlでいうオブジェクトとはリファレンスである
- リファレンスとは参照である
- 変数のリファレンスを得るには、変数の前にバックスラッシュ「\」を置くことで得られる
- さらに **祝福する(bless)** ことによって、オブジェクトは自身が所属するクラスを記憶する
- 通常コンストラクタでblessして、返却する
- 8割がたハッシュのリファレンスがインスタンスとして使用される
 - もちろん配列のリファレンス、スカラ変数のリファレンスもインスタンスとなりうる

```ruby
# ruby
class Hoge
end
Hoge.new.class #=> Hoge
```
```perl
# perl
package Hoge {
  sub new {
    # bless(リファレンス, クラス名)、{}は無名ハッシュリファレンス、__PACKAGE__は現クラス名
    my $self = bless {}, __PACKAGE__;
    return $self;
  }
}
# 組み込み関数refでリファレンスの所属クラス名を知る
ref Hoge->new #=> Hoge
```

### クラスメソッド、インスタンスメソッド

- クラスメソッド・インスタンスメソッドの呼び出し方は「->」
- **「->」で呼び出すと@_の先頭に呼び出し元が自動的に格納される**
- **「->」で呼び出すと@_の先頭に呼び出し元が自動的に格納される**
- **「->」で呼び出すと@_の先頭に呼び出し元が自動的に格納される**
- 重要なことなので3回書きました

```ruby
# ruby
class Hoge
  def initialize(*args)
    p args
  end
  def hoge(*args)
    p args
  end
end
Hoge.new(1, 2, 3).hoge(4, 5, 6)
#=> [1, 2, 3]
#=> [4, 5, 6]
```
```perl
# perl
package Hoge {
  sub new {
    warn "@_";
    return bless {}, __PACKAGE__;
  }
  sub hoge {
    warn "@_";
  }
}
Hoge->new(1, 2, 3)->hoge(4, 5, 6);
#=> Hoge 1 2 3 at - line 3.
#=> Hoge=HASH(0x7fcaf9026680) 4 5 6 at - line 7.
# リファレンスを見ようとすると「クラス名=リファレンス元(メモリアドレス)」の表示になる
```
