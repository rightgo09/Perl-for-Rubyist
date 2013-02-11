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
