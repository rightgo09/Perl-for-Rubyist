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
