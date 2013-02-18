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

