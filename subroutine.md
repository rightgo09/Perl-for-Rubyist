サブルーチン(メソッド)
--------

### サブルーチン
- Perlではメソッドや関数のことを「サブルーチン」という
- コンテキストによって「メソッド」と呼ぶこともある（主にオブジェクトが呼び出す場合）
- 定義は組み込み関数「sub」によって行う
- サブルーチンは宣言したpackage名前空間に属する

```ruby
# ruby
def hoge
  "hoge!"
end

class Fuga
  def fuga
    "fuga!"
  end
end

hoge() #=> "hoge!"
Fuga.new.fuga() #=> "fuga!"
```
```perl
# perl
sub hoge {
  return "hoge!";
}

package Fuga {
  sub fuga {
    return "fuga!";
  }
}

hoge(); #=> "hoge!"
Fuga::fuga(); #=> "fuga!"
```

### 引数
- Perlは引数の定義が存在しない
- 引数として渡される値はすべて特殊変数「@_」に格納される
- @のシジルが付いていることらもわかるように、配列である
- @_の要素は引数のエイリアスであり、変更するとコール側でも変更されてしまう
- 通常、サブルーチンの先頭で別の変数にコピーして使用する

```ruby
# ruby
def hoge(arg1, arg2)
  "arg1: #{arg1}, arg2: #{arg2}"
end
hoge("foo", "bar") #=> "arg1: foo, arg2: bar"
```
```perl
# perl
sub hoge {
  my ($arg1, $arg2) = @_;
  return "arg1: $arg1, arg2: $arg2";
}
hoge("foo", "bar"); #=> "arg1: foo, arg2: bar"
```
