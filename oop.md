オブジェクト
--------

- Rubyのように、「数値を書いたら」「文字列を置けば」それがオブジェクト **というわけではない** 。 **全くない**
- **もっと頑張る必要がある**
- 最初に自前で作る大変さを経験すると、あとでライブラリ使って「なにこれすごい！」と感動できる

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

#### クラス名

- Perlでも「::」は名前空間のセパレータとして使われるが、もっとゆるい
- いきなり「Hoge::Fuga::Foo::Bar」というクラス名で宣言可能（Rubyのようにネストして宣言しなくてよい）
- 基本的に、「::」で区切る最後の部分がファイル名になり、以前がディレクトリ名になる
- **ファイル名は小文字に変換したりせず** 、「クラス名.pm」
- useやrequireのロード時には、「::」はディレクトリセパレータとしても解釈される

lib/hoge/fuga/foo/bar.rb
```ruby
# ruby
class Hoge
  class Fuga
    class Foo
      class Bar
      end
    end
  end
end
Hoge::Fuga::Foo::Bar.new
```
lib/Hoge/Fuga/Foo/Bar.pm
```perl
# perl
package Hoge::Fuga::Foo::Bar {
}
# ex. Hoge::Fuga::Foo::Bar->new()
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

### セッタ・ゲッタ

- attr_accessorなんてものは（標準では）ない
- 自作だとかなりだるい

```ruby
# ruby
class Hoge
  attr_accessor :foo   # セッタ・ゲッタ
  def initialize(foo)
    @foo = foo
  end
end
hoge = Hoge.new(123)
hoge.foo        #=> 123
hoge.foo = 456
hoge.foo        #=> 456
```
```perl
# perl
package Hoge {
  sub new {
    my ($class, $foo) = @_;
    return bless { foo => $foo }, __PACKAGE__;
  }
  sub foo {
    my $self = shift(@_);
    # まだ引数がある ==> セッタとして扱う
    if (@_) {
      $self->{foo} = shift(@_); # ハッシュリファレンスの元値を更新
    }
    # 引数がない ==> ゲッタとして扱う
    else {
      return $self->{foo}
    }
  }
  # もっと短く！
  # sub foo { @_ > 1 ? $_[0]->{foo} = $_[1] : $_[0]->{foo} }
}
my $hoge = Hoge->new(123);
$hoge->foo;        #=> 123
$hoge->foo(456);   # Rubyの=のような糖衣構文（左辺代入）は用意しないと使えない、基本的に引数として渡す
$hoge->foo;        #=> 456
```

- 実はこの手作りクラスだと、 **クラスの外側でセッタを通さずに直接プロパティの更新が可能である**
- 性・善・説

```perl
my $hoge = Hoge->new(123);
$hoge->{foo} = 456;   # えっ！
$hoge->foo; #=> 456   # えっ！！！！！
```

### public / private

- Perlでは、クラス内のメソッドは、 **すべてpublicである**
- privateにするには、メソッドとしてではなく、メソッドのリファレンスをクラス内からしか見えない変数に入れることによって、まあ実現できる
- 基本はでもpublic

```ruby
# ruby
class Hoge
  def foo; bar; end
  def bar; 123; end
  public  :foo
  private :bar
end
hoge = Hoge.new
hoge.foo #=> 123
hoge.bar # NoMethodError: private method `bar' called for #<Hoge:0x007f8c530a1130>
```
```perl
# perl
package Hoge {
  sub new { bless {}, __PACKAGE__ }
  # クラス内レキシカル変数に無名サブルーチンのリファレンスを格納、レキシカル変数はクラス外から参照できない
  my $bar = sub { 123 };
  sub foo { $bar->() }  # サブルーチンリファレンスの実行
}
my $hoge = Hoge->new;
$hoge->foo; #=> 123
$hoge->bar; # Can't locate object method "bar" via package "Hoge"
```

### 継承

- 特殊変数@ISAに親クラス名を入れるだけ（かんたん！）
- 基本は「baseモジュール」経由で継承する、もしくは「parentモジュール」

```perl
package Hoge {
  sub new { bless {}, shift(@_) }
}
package Fuga {
  our @ISA = ('Hoge'); # myではなくourでクラスに紐づく変数として使う
}
my $fuga = Fuga->new;
```

- Perlは多重継承ができる
- 多重継承の場合、メソッド探索は深さ優先になる

```perl
package Hoge {}
package Fuga {}
package Piyo { sub new { bless {}, shift(@_) } }
package Foo {
  our @ISA = ('Hoge', 'Fuga', 'Piyo');
}
my $foo = Foo->new;
```

ライブラリによるオブジェクト指向
--------

- 手作りの限界。「そうだ、CPANに行こう」
- 鉄板
 - Class::Accessor::*
 - Moose / Mouse / Moo

```ruby
# ruby
class Hoge
  attr_accessor :foo
  def initialize(foo)
    @foo = foo
  end
end
hoge = Hoge.new(123)
hoge.foo #=> 123
```

### Class::Accessor::Fast

```perl
package Hoge {
  use base 'Class::Accessor::Fast'; # Class::Accessor::Fastを継承
  __PACKAGE__->mk_accessors('foo'); # クラスメソッドを呼び出す（メソッドを親クラスへ探索しに行く）
}
my $hoge = Hoge->new({ foo => 123 });
$hoge->foo; #=> 123
```

### Moo

```perl
package Hoge {
  use Moo; # strict/warnings効果もある
  # DSL
  has 'foo' => (
    is => 'rw',  # read/write
  );
}
my $hoge = Hoge->new({ foo => 123 });
$hoge->foo;       #=> 123
$hoge->foo(456);
$hoge->foo;       #=> 456
```

