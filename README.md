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
