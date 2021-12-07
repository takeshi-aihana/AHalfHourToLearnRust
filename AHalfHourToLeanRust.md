```
1234567890123456789012345678901234567890123456789012345678901234567890123456789012345678901234567890
---------+---------+---------+---------+---------X---------+---------+---------+---------+---------X
$Lastupdate: 2021/12/06 10:46:04 $ T.AIHANA
```

* [A half-hour to lean Rust](https://fasterthanli.me/articles/a-half-hour-to-learn-rust)

# 30分で学ぶ Rust

## はじめに

プログラミング言語の「流暢さ」を向上させるためには、その言語のプログラムをたくさん読む必要があります。
でも何も知らないのに、どうすればたくさん読むことができるのでしょうか？

この記事では、Rust の概要をかいつまんでいくのではなく、できるだけたくさんの Rust の「スニペット」を調べて、それらに含まれるキーワードや記号の意味を解説していきます。

準備はいいですか？
では始めましょう！

----

## ``let`` キーワード


``let`` は変数名を一つ宣言します:

```Rust
let x;    // "x" という変数名を宣言する
x = 42;    // 数値の 42 を "x" という変数に割り当てる
```

これは一行で書くこともできます：

```Rust

let x = 42;
```

記号 ``:``（コロン）を使って変数の型を明示的に指定できます（この記号は「型の注釈（*annotation*）」を表します）：

```Rust
let x: i32;    // `i32` は符号付き 32-bit 整数型である
x = 42;

// 注釈できる型は i8, i16, i32, i64, i128 があります
// 他に符号なしだと u8, u16, u32, u64, u128 があります
```

これも一行で書くことができます：

```Rust
let x: i32 = 42;
```

変数名を宣言したあとでその変数を初期化すると、コンパイラは初期化が完了するまで変数を利用できないようにします。

```Rust
let x;
foobar(x);    // error: borrow of possibly-uninitialized variable: `x`
x = 42;
```

ただし、次の書き方は全く問題ありません：

```Rust
let x;
x = 42;
foobar(x);    // ここから変数 `x` の型がコンパイラによって推測される
```

記号 ``_``（アンダースコア）は特別な変数名です。
と言うよりは「名前無し」の変数です。もともとは何かを「捨てる」という意味があります：

```Rust
// これは「何もしません」（なぜなら `42` は定数だからです）
let _ = 42;

// これは `get_thing()` を呼び出しますが、返り値は破棄されます
let _ = get_thing()
```

``_`` で始まる名前は通常の変数名で、単にコンパイラがそれらが使用されていないことを警告しないだけです：

```Rust
// 最終的にどこかで変数 `_x` を使うかもしれないが、このプログラムは「まだ開発中」のものなので
// 今のところはコンパイラには警告を無視してもらう（という意図がある）
let _x = 42;
```

同じ名前を別個に宣言することもできます。
すなわち変数の宣言を不可視（*shadow*）にできます：

```Rust
let x = 13;
let x = x + 3;    // 上の行のあとで `x` を使うと二番目の` x` だけが参照される
                  // （すなわち最初の `x` はもう存在しない）
```

Rust にはタプル（*Tuple*）があります。
タプルは「さまざまな型の値を要素とする固定長のコレクション型」です。

```Rust
let pair = ('a', 17);
pair.0;    // これは 'a'
pair.1;    // これは 17
```

変数 `pair` の型にきちんと注釈を付けたいのであれば、次のように書きます：

```Rust
let pair: (char, i32) = ('a', 17);
```

タプルは値を割り当てると「分解（*destructured*）」される可能性があります。
すなわちタプルという集合体が個々の要素に分割されるということです：

```Rust
let (some_char, some_int) = ('a', 17);
// ここで `some_char` は `a`、`some_int` は 17 になる
```

この仕様は、特に関数からタプルを返す際に便利です：

```Rust
let (left, right) = slice.split_at(middle);
```

もちろんタプルを分解する際に記号 `_` を使って一部の要素を破棄することもできます：

```Rust
let (_, right) = slice.split_at(middle);
```

記号 `;`（セミコロン）は文の終端を意味します：

```Rust
let x = 3;
let y = 5;
let z = y + x;
```

また、文は複数行にまたがって書くことも可能です：

```Rust
let x = vec![1, 2, 3, 4, 5, 6, 7, 8]
    .iter()
    .map(|x| x + 3)
    .fold(0, |x, y| x + y)
```

（これらが実際に何を意味しているかについては、後ほど説明します）

----

## `fn` キーワード

`fn` は関数を一つ宣言します。
次は void 関数の宣言です：

```Rust
fn greet() {
    println!("Hi there!");
}
```

そして、次は一個の 32-bit 符号付き整数値を返す関数の宣言です。
記号 `->` は関数の返り値の型を示しています：

```Rust
fn fiar_dice_roll() -> i32 {
    4
}
```

中カッコのペア `{` と `}` はブロックを宣言するもので、ブロックはそれ専用の「スコープ」を持ちます：

```Rust
// この例は "in" を出力してから "out" を出力します
fn main() {
    let x = "out"
    {
        // これは別の `x` です
        let x = "in";
        println!("{}", x);
    }
    println!("{}", x);
}
```

ブロックも「式」です。
これは値を評価すると言う意味です。

```Rust
// これは:
let x = 42;

// 次と同じです：
let x = { 42 };

```

ブロックの中に複数の文を書くこともできます：

```Rust
let x = {
    let y = 1;    // 最初の文
    let z = 2;    // 二番目の文
    y + z    // これが *最後* の文 ー ブロック全体で評価するもの
};
```

そして「関数の最後で記号 `;` を省略する」ことは関数から返ると同じ意味になります。
すなわち、次の二つの関数は同じです：

```Rust
fn fair_dice_roll() -> i32 {
    return 4;
};

fn fair_dice_roll() -> i32 {
    4
};
```

条件文のキーワード `if` もまた式です：

```Rust
fn fair_dice_roll() -> i32 {
    if feeling_lucky {
        6
    } else {
        4
    }
}
```

キーワード `match` も式です：

```Rust
fn fair_dice_roll() -> i32 {
    match feeling_lucky {
        true => 6,
        false => 4,
    }
}
```

記号 `.`（ドット）は通常は一個の値が持ついくつかのメンバ（*field*）にアクセスする際に使用します：

```Rust
let a = (10, 20);
a.0;    // これは `10` です

let amos = get_some_struct();
amos.nickname;    // これは "fasterthanlime" です
```

または値に対してメソッドを呼び出す際に使います：

```Rust
let nick = "fasterthanlime";
nick.len();    // これは 14 です
```

記号 `::`（ダブルコロン）も似ていますが、これは名前空間（*namespace*）を操作するものです。
次の例では、`std` はクレート名（*crate*、ライブラリ）、`cmp` はモジュール名（ソースファイル）、そして `min` が関数名とすると：

```Rust
let least = std::cmp::min(3, 8);    // これは 3
```

命令 `use` は他の名前空間からいくつかの名前を「スコープの中に取り込む（インポートする）」際に使用します：

```Rust
use std::cmd::min;

let least = min(7, 1);    // これは 1 です
```

命令 `use` の内側では中カッコ `{` と `}` には別の意味になります：
これらは「塊（*globs*）」と言う意味です。
ここで、もし関数 `min` と `max` の両方をスコープの中にインポートしたい場合、次のいずれかの書き方が可能です：

```Rust
// これは動作します：
use std::cmp::min;
use std::cmp::max;

// これも動作します：
use std::cmp::{ min, max };

// これも動作するんです！
use std::{ cmp::min, cmp::max };
```

記号 ``*``（ワイルドカード）は名前空間からすべてのシンボルをインポートしてくれます：

```Rust
// これはスコープ内にある `min` と `max` を取り込み、さらに他のシンボルもたくさんとりこみます
use std::cmap::*;
```

「型」も名前空間です。
そしてメソッドを通常の関数のように呼び出すことができます：

```Rust
let x = "amos".len();    // これは 4 です
let x = str::len("amos");    // これも 4 です
```

この ``str`` 型は基本型の一つですが、デフォルトのスコープの中には基本型ではない型もたくさんあります。

```Rust
// `Vec` 型は通常の構造体であって、基本型ではありません
let v = Vec::new();

// これは上と同じことをするコードですが、`Vec` に名前空間の「完全なパス」がついたものになっています
let v = std::vec::Vec::new();
```

Rust がすべてのモジュールの先頭で名前空間の完全なパスを補完してくれるので、前者のような書き方でも動作するというわけです：

```Rust
use std::prelude::v1::*;
```

（この仕組みにより、`Vec` や `String`、`Option`、そして `Result` といったたくさんのシンボルが再エクスポートされます）。


構造体の場合は `struct` キーワードを付けて宣言します：

```Rust
struct Vec2 {
    x: f64,    // 64-bit の浮動小数点型（いわゆる倍精度）
    y: f64,
}
```

これらは *struct literals* を使って初期化することができます：

```Rust
let v1 = Vec2 { x: 1.0, y: 3.0 };
let v2 = Vec2 { y: 2.0, x: 4.0 };
// メンバを書く順番は関係ありません（名前だけ合わせて下さい）
```

別の構造体を使って、残りのメンバを初期化させる「ショートカット」があります：

```Rust
let v3 = Vec2 {
    x: 14.0,
    ..v2
};
```

これは "*struct update syntax*" と呼ばれているもので、構造体で最後のメンバに対してのみ指定でき、その後ろに記号 `,`（コンマ）を付けないで下さい。

これにより残りのメンバはすべてのメンバを意味する可能性があることに注意して下さい：

```Rust
let v4 = Vec2 { ..v3 };
```

構造体は、タプルと同様に、そのメンバを分解することができます。

前述のタプルのような書き方も `let` では問題ありません：

```Rust
let (left, right) = slice.split_at(middle);
```

そのため、これは：

```Rust
let v = Vec2 { x: 3.0, y: 6.0 };
let Vec2 { x, y } = v;
// ここで `x` は 3.0、`y` は `6.0` になります
```

そして、これは：

```Rust
let Vec2 {x, ..} = v;
// これはメンバ `v.y` を破棄します
```

`let` 場合、`if` の中で条件文としても利用できます：

```Rust
struct Number {
    odd: bool,
    value: i32,
}

fn main() {
    let one = Number { odd: true, value: 1 };
    let two = Number { odd: false, value: 2 }
    print_number(one);
    print_number(two);
}

fn print_number(n: Number) {
    if let Number { odd: true, value } = n {
        println!("Odd number: {}", value);
    } else if let Number { odd: false, value } = n {
        println!("Even number: {}", value);
    }
}

// これを実行したときの出力は：
// Odd number: 1
// Even number: 2
```

キーワード `match` は「網羅的」（*exhaustive*）であるべきです：
すなわち、少なくとも一つの要素は一致させる必要があります。

```Rust
fn print_number(n: Number) {
    match n {
        Number { value: 1, .. } => println!("One"),
        Number { value: 2, .. } => println!("Two"),
        Number { value, .. } => println!("{}", value),
        // もし最後の文が存在していなければ、コンパイル時にエラーとなります
    }
}
```

それが難しい場合は、記号 `_`（アンダースコア）を「一時的な物置き」（*catch-all*）として使って下さい：

```Rust
fn print_number(n: Number) {
    match n.value {
        1 => println!("One"),
        2 => println!("Two"),
        _ => println!("{}", n.value),
    }
}
```

----

## 独自の型

独自の型でいろいろなメソッドを宣言することができます：

```Rust
struct Number {
    odd: bool,
    vlaue: i32,
}

impl Number {
    fn is_strictly_positive(self) -> bool {
        self.value > 0
    }
}
```

そして、いつものようにそれらのメソッドを使って下さい：

```Rust
fn main() {
    let minus_two = Number {
        odd: false,
        vlaue: -2,
    };
    println!("positive? {}", minus_two.is_strictly_positive());
    // これは "positive? false" と出力されます
}
```

変数へ代入（*variable binding*）はデフォルトで不変（*immutable*）になります。
すなわち、変数の中身を変更することはできません：

```Rust
fn main() {
    let n = Number {
        odd: true,
        value: 17,
    };
    n.odd = false;    // error: cannot assign to `n.odd`
                      // その理由は `n` が変更可能（mutable）で宣言されていないからです
}
```

また変数そのものに代入することもできません：

```Rust
fn main() {
    let n = Number {
        odd: true,
        value: 17,
    };
    n = Number {
        odd: false,
        value: 22,
    };    // error: cannot assign twice to immutable variable `n`
}
```

キーワード `mut` は変数の中身を変更可能なものにします：

```Rust
fn main() {
    let mut n = Number {
        odd: true,
        vlaue: 17,
    }
    n.value = 19;    // これは問題ありません
}
```

## トレイト

「トレイト」（`trait`）とは複数の型に共通する振る舞いです（他の言語の「インタフェース」に似ています。トレイトを使うことで同じ名前の機能を様々な型に実装・追加できます。）：

```Rust
trait Signed {
    fn is_strictly_negative(self) -> bool;
}
```

トレイトに関しては以下を実装できます：

  - 誰かの型に貴方のトレイトの一つ（*one of your traits on anyone's type*）
  - 貴方の型の一つに誰かのトレイト（*anyone's trait on one of your types*）
  - ただし貴方以外の型に貴方以外のトレイトは不可（*but not a foreign trait on a foreign type*）


これらは「孤立のルール」（*orphan rules*）と呼ばれています。


次は自分たちの型に自分たちのトレイトを実装した例です：

```Rust
impl Signed for Number {
    fn is_strictly_negative(self) -> bool {
        self.value < 0
    }
}

fn main() {
    let n = Number { odd: false, value: -44 };
    println!("{}", n.is_strictly_negative());    // この出力は "true"
}
```

次は自分たちの型に無関係なトレイトを実装した例です：

```Rust
// `Neg` と言うトレイとは単項マイナス演算子の `-` を
// オーバーロードする際に使用されます
impl std::ops::Neg for Number {
    type Output = Number;
    
    fn neg(self) -> Number {
        Number {
            value: -self.value,
            odd: self.odd,
        }
    }
}

fn main() {
    let n = Number { odd: true, value: 987 }
    let m = -n;    // これだけは可能（なぜなら `Neg を実装したのは自分たちなので）
    println!("{}", m.value);    // この出力は "-987"
```

キーワード `impl` のブロックは「型」に対するものなので、このブロックの中で `Self` は対応する型に相当します：

```Rust
impl std::ops::Neg for Number {
    type Output = Self;
    
    fn neg(self) -> Self {
        Self {
            value: -self.value,
            odd: self.odd,
        }
    }
}
```

一部のトレイトは「マーカ」（*marker*）です。
これは型がメソッドを実装しているのではなく、特定のことを型で実行できるという意味です。

例えば `i32` と言う型は `Copy` というトレイトを実装しています（要するに `i32` 型は `Copy` ということです）。
これは次のような動きになります：

```Rust
fn main() {
    let a: i32 = 15;
    let b = a;    // `a` がコピーされる
    let c = a;    // また `a` がコピーされる
}
```

さらに次のような動きもあります：

```Rust
fn print_i32(x: i32) {
    println!("x = {}", x);
}

fn main() {
    let a: i32 = 15;
    print_i32(a);    // `a` がコピーされる
    print_i32(a);    // また `a` がコピーされる
}
```

ただし `Number` という（独自の）構造体は `Copy` というトレイトを実装していません。
そのため上のような動きにはならずコンパイル・エラーになります：

```Rust
fn main() {
     let n = Number { odd: true, value: 51 };
     let m = n;    // `n` が `m` の中に移動する
     let o = n;    // error: use of moved value: `n`（すでに移動しているので、これ以上は移動できない）
}
```

さらに、次も同様にコンパイル・エラーになります：

```Rust
fn print_number(n: Number) {
    println!("{} number {}", if n.odd { "odd" } else { "even" }, n.value);
}

fn main() {
    let n = Number { odd: true, value: 51 };
    print_number(n);    // `n` が移動する
    print_number(n);    // error: use of moved value: `n` （上と同じ理由）
}
```

そのかわり、関数 `print_number()` が変更不可（*immutable*）な参照を受け取る場合はコンパイル・エラーにはなりません：

```Rust
fn print_number(n: &Number) {
    println!("{} number {}", if n.odd { "odd" } else { "even" }, n.value);
}

fn main() {
    let n = Number { odd: true, value: 51 };
    print_number(&n);    // `n` は関数呼び出で一時的に貸し出される
    print_number(&n);    // また `n` を関数呼び出で一時的に貸し出される
}
```

さらに、この関数が変更可能（*mutable*）な参照を受け取った場合もコンパイル・エラーにはなりません。ただし変数への代入でもキーワード `mut` が必要になります：

```Rust
fn invert(n: &mut Number) {
    n.value = -n.value;
}
    
fn print_number(n: &Number) {
    println!("{} number {}", if n.odd { "odd" } else { "even" }, n.value);
}

fn main() {
    // この場合 `n` を変更可能な変数にする
    let mut n = Number { odd: true, value: 51 };
    print_number(&n);
    invert(&mut n);    // `n` は変更可能な変数として貸し出される
    print_number(&n);
}
```

トレイトとして実装したメソッドは参照または変更可能な参照を使えば `self` も受け取れるようになります：

```Rust
impl std::clone::Clone for Number {
    fn clone(&self) -> Self {
        Self { ..*self }
    }
}
```

このメソッドを呼び出すと、呼び出し側が「暗黙的」に貸し出されます：

```Rust
fn main() {
    let n = Number { odd: true, value: 51 };
    let mut m = n.clone();
    m.value += 100;
    
    println!(&n);
    println!(&m);
}
```

すこし詳しく説明すると、次の書き方は等価です：

```Rust
let m = n.clone();

let m = std::clone::Clone::clone(&n);
```

`Copy` のようなマーカのトレイトにはメソッドはありません：

```Rust
// メモ: `Copy` には `Clone` も実装されている必要があります
impl std::clone::Clone for Number {
    fn clone(&self) -> Self {
        Self { ..*self }
    }
}

impl std::marker::Copy for Number {}
```

この状態でも引き続き `Clone` を利用できますが：

```Rust
fn main() {
    let n = Number { odd: true, value: 51 };
    let m = n.clone();
    let o = n.clone();
}
```

`Number` の値は移動されなくなります：

```Rust
fn main() {
    let n = Number { odd: true, value: 51 };
    let m = n;    // `m` は `n` のコピーです
    let o = n;    // これも同じで、`n` は移動されることも貸し出されることもありません
}
```

一部のトレイトはごくごく普通に使われるので、`derive` という属性を使って自動的に実装させることが可能になっています：

```Rust
#[derive(Clone, Copy)]
struct Number {
    odd: bool,
    value: i32,
}

// 上の定義は `impl Clone for Number` と `impl Copy for Number`  のブロックに展開されます
```

関数はジェネリック（*generic*）型（特定の型に依存しない実装すにするための書き方）で記述できます：

```Rust
fn foobar<T>(arg: T) {
    // `arg` を使って何か処理する
}
```

これらの関数は複数の「型パラメータ」（*type parameter*）を持たせ、具体的な型の代わりに、関数の宣言とその実装の中で使用することができるようになっています：

```Rust
fn foobar<L, R>(left: L, right: R) {
    // `left` と `right` を使って何か処理する
}
```

通常、型パラメータには「制約」（*constraint*）があるので、それらを使って実際に何か処理することができます。

もっとも単純な制約はトレイトの名前です：

```Rust
fn print<T: Display>(value: T) {
    println!("value = {}", value);
}

fn print<T: Debug>(value: T) {
    println!("value = {:?}", value);
}
```

型パラメータの制約には長い構文で書く方法があります。次の制約：

```Rust
fn print<T: Display>(value: T) {
    println!("value = {}", value);
}
```

は次と同じ意味を持ちます：

```Rust
fn print<T>(value: T)
where
    T: Display,
{
    println!("value = {}", vlaue);
}
```

制約はより複雑になる場合があります。
すなわち、制約で多重トレイト（*multiple traits*）を実装するために型パラメータが必要になる場合があります：

```Rust
use std::fmt::Debug;

fn compare<T>(left: T, right: T)
where
    T: Debug + PartiqlEq,
{
    println!("{:?} {} {:?}", left, if left == right { "==" } else { "!=" }, right);
}

fn main() {
    compare("tea", "coffee");
    // この出力は： "tea" != "coffee"
}
```

ジェネリック型の関数とは、さまざまな型を持つ無限個の関数を含んだ名前空間と考えることができます。

いろいろなクレート、モジュール、そして型と同様に、ジェネリック型の関数は記号 ``::`` を使って「順にたどる」（探検する？）ことが可能です：

```Rust
fn main() {
    use std::any::type_name;
    println!("{}", type_name::<i32>());    // 出力は "i32"
    println!("{}", type_name::<(f64, char)>());    // 出力は "(f64, char)"
```

この記号は愛情をこめて [turbofish 構文](https://turbo.fish/) と呼んでいます（なぜなら ``::<>`` の記号が魚に見えるからです）。

構造体もジェネリック型にすることができます：

```Rust
struct Pair<T> {
    a: T,
    b: T,
}

fn print_type_name<T>(_val: &T) {
    println!("{}", std::any::type_name::<T>());
}

fn main() {
    let p1 = Pair { a: 3, b: 9 };
    let p2 = Pair { a: true, b: false};
    print_type_name(&p1);    // 出力は "Pair<i32>"
    print_type_name(&p2);    // 出力は "Pair<boo>"
}
```

標準ライブラリにある ``Vec``（ヒープ領域に確保される配列）型はジェネリック型です：

```Rust
fn main() {
    let mut v1 = Vec::new();
    v1.push(1);
    let mut v2 = Vec::new();
    v2.push(false);
    print_type_name(&v1);    // 出力は "Vec<i32>"
    print_type_name(&v2);    // 出力は "Vec<boo>"
}
```

``Vec`` 型と言えば、多かれ少なかれ「vec リテラル」を与えるマクロが含まれています：

```Rust
fn main() {
    let v1 = vec![1, 2, 3];
    let v2 = vec![true, false, true];
    print_type_name(&v1);    // 出力は "Vec<i32>"
    print_type_name(&v2);    // 出力は "Vec<boo>"
}
```

``name!()``、``name![]``、あるいは ``name!{}`` のすべてがマクロを呼ぶようになっています。
これらのマクロは通常のコードに展開されるだけです。

実際のところ ``println`` もマクロです：

```Rust
fn main() {
    println!("{}", "Hello there!");
}
```

これは次と同じ効果を持つコードに展開されます：

```Rust
fn main() {
    use std::io::{self, Write};
    io::stdout().lock().write_all(b"Hello there!\n").unwrap();
}
```

キーワード ``panic`` もマクロの一種です。
これが有効になっている場合、エラーメッセージの他に、エラーが発生したファイル名とその行番号を出力してプログラムの実行を完全に停止（パニック）します：

```Rust
fn main() {
    panic!("This panics");
}
// このとき出力は： thread 'main' panicked at 'This panics', src/main.rs:3:5
```

他のメソッドにもパニックするものがあります。
例えば、``Option`` 型に何かを含めることが可能ですし、もしくは何も含めなくても構いません。
もし、この型で ``.unwrap()`` メソッドが呼び出され、その型に何も含まれていないとパニックが発生します：

```Rust
fn main() {
    let o1: Option<i32> = Some(128);
    o1.unwrap();    // これは問題なし

    let o2: Option<i32> = None;
    o2.unwrap();    // これはパニックになります！
}

// このとき出力は： thread 'main' panicked at 'called `Option::unwrap()` on a `None` value', src/libcore/option.rs:378:21
```

``Option`` 型は構造体ではありません ー
二つの可変値を持つ ``enum``（列挙子）です。

```Rust
enum Option<T> {
    None,
    Some(T),
}

impl<T> Option<T> {
    fn unwrap(self) -> T {
        // 列挙子にある二つの可変値はいろいろなケースで利用できます：
        match self {
            Self::Some(t) => t,
            Self::None => panic!(".unwrap() called on a None option"),
        }
    }
}

use self::Option::{None, Some};

fn main() {
    let o1: Option<i32> = Some(128);
    o1.unwrap();    // これは問題なし

    let o2: Option<i32> = None;
    o2.unwrap();    // これはパニックになります！
}

// このときの出力は： thread 'main' panicked at '.unwrap() called on a None option', src/main.rs:11:27
```

``Result`` も ``enum`` の一種で、何かが含まれているか、もしくはエラーが含まれているかのどちらかになります：

```Rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

これも ``.unwrap()`` メソッドが呼ばれた際、エラーの他に何も含まれていないとパニックになります。


## ライフタイム

変数への割当てでは「ライフタイム」（賞味期限）があります：

```Rust
fn main() {
    // ここでは `x` はまだ存在していません
    {
        let x = 42;    // `ここから `x` が存在します
        println!("{}", x);
        // ここで `x` が無くなります
    }
    // ここでは `x` はもう存在していません
}
```

同様に、参照にもライフタイムがあります：

```Rust
fn main() {
    let x_ref = {
        let x = 42;
        &x
    };
    println!("x_ref = {}", x_ref);
    // エラーになります：
    // error: `x` does not live long enough
}
```

参照の変数への代入では不変（*immutable*）の状態であれば複数回借用できます：

```Rust
fn main() {
    let x = 42;
    let x_ref1 = &x;
    let x_ref2 = &x;
    let x_ref3 = &x;
    println!("{} {} {}", x_ref1, x_ref2, x_ref3);
}
```

したがって参照を借用している間、変更可能な状態での変数への代入はできません：

```Rust
fn main() {
    let mut x = 42;
    let x_ref = &x;
    x = 13;
    println!("x_ref = {}", x_ref);
    // エラーになります：
    // error: cannot assign to `x` because it is borrowed
}
```

また参照を変更不可の状態（*immutable*）で借用中に、変更可能な状態（*mutable*）の変数を借用することはできません：

```Rust
fn main() {
    let mut x = 42;
    let x_ref1 = &x;
    let x_ref2 = &mut x;
    // エラーになります：
    // error: cannot borrow `x` as mutable because it is also borrowed as immutable
    println!("x_ref1 = {}", x_ref1);
}
```

関数の引数として渡した参照にもライフタイムがあります：

```Rust
fn print(x: &i32) {
    // `x` は、この関数が呼ばれた時にだけ（関数の外から）借用されもの
}
```

このように参照を引数として受け取る関数では、さまざまなライフタイムを持つもので呼び出すことができます。
そのため：

  - 参照を受け取るすべての関数はジェネリック（*generic*）型で記述する
  - ライフタイムはジェネリック型の引数となる

ライフタイムの名前はシングル・クオーテーション ``'`` で始まります：

```Rust
// ライフタイムを考慮していない引数（名前がない）
fn print(x: &i32) {}

// ライフタイムの名前がついた引数
fn print<'a>(x: &'a i32) {}
```

これにより、引数のライフタイムに応じたライフタイムを持つ参照を返すことができるようになります：

```Rust
struct Number {
    value: i32,
}

fn number_value<'a>(num: &'a Number) -> &'a i32 {
    &num.value
}

fn main() {
    let n = Number { value: 47 };
    let v = number_value(&n);
    // `v` は `n` を（不変な状態で）借りることになるので： `v` は `n` よりも「長生き」できません
    // `v` が「生きている」間、`n` を変更可能な状態で借りたり、変更可能にしたり、移動することはできません
}
```

引数として受け取るライフタイムが「一つしかない」のであれば名前を付ける必要はありません。
そして、全て同じライフタイムになります。
そのため、次の二つの関数は等価です：

```Rust
fn number_value<'a>(num: &'a Number) -> &'a i32 {
    &num.value
}

fn number_value(num: &Number) -> &i32 {
    &num.value
}
```

構造体も「ライフタイムを超えたジェネリック型」（*generic over lifetimes*）にすることができます。
これを使うといろいろな参照を保持できるようになります：

```Rust
struct NumRef<'a> {
    x: &'a i32,
}

fn main() {
    let x: i32 = 99;
    let x_ref = NumRef { x: &x };
    // `x_ref` は `x` よりも「長生き」できません、云々
}
```

次は上と等価なコードですが、関数が一つあいだに追加されています：

```Rust
struct NumRef<'a> {
    x: &'a i32,
}

fn as_num_ref<'a>(x: &'a i32) -> NumRef<'a> {
    NumRef { x: &x }
}

fn main() {
    let x: i32 = 99;
    let x_ref = as_num_ref(&x);
    // `x_ref` は `x` よりも「長生き」できません、云々
}
```

さらに、次は上と等価なコードですが、ライフタイムの省略版です：

```Rust
struct NumRef<'a> {
    x: &'a i32,
}

fn as_num_ref(x: &i32) -> NumRef<'_> {
    NumRef { x: &x }
}

fn main() {
    let x: i32 = 99;
    let x_ref = as_num_ref(&x);
    // `x_ref` は `x` よりも「長生き」できません、云々
}
```

キーワード ``impl`` で囲まれたブロックも「ライフタイムを超えたジェネリック型」（*generic over lifetimes*）になれます：

```Rust
impl<'a> NumRef<'a> {
    fn as_i32_ref(&'a self) -> &'a i32 {
        self.x
    }
}

fn main() {
    let x: i32 = 99;
    let x_num_ref = NumRef { x: &x };
    let x_i32_ref = x_num_ref.as_i32_ref();
    // `x_num_ref` と `x_i32_ref` のどちらも `x` よりも「長生き」できません
}
```

ただしライフタイムの省略版にすることはできます：

```Rust
impl<'a> NumRef<'a> {
    fn as_i32_ref(&self) -> &i32 {
        self.x
    }
}
```

ライフタイムに名前が必要ない場合は、もっと省略することができます：

```Rust
impl NumRef<'_> {
    fn as_i32_ref(&self) -> &i32 {
        self.x
    }
}
```

``static`` という特別なライフタイムがあります。
これはプログラムの最初から最後までのライフタイムを有効にします。

文字列は '``static``' のライフタイムを持ちます：

```Rust
struct Person {
    name: &'static str,
}

fn main() {
    let p = Person {
        name: "fasterthanlime",
    };
}
```

ただし自らが作り所有する一時的な文字列（*owned strings*）は '``static``' にはなりません：

```Rust
struct Person {
    name: &'static str,
}

fn main() {
    let name = format!("fasterthanlime{}", "lime");
    let p = Person { name: &name };
    // これはエラーになります
    // error: `name` does not live long enough
}
```

上の例でローカル変数の ``name`` は ``&'static str`` 型ではなく ``String`` 型です。
この ``name`` は動的に確保され、あとで解放されます。
実際のところ、この ``name`` のライフタイムはプログラムのライフタイムよりも短いです（それがたまたま ``main()`` と同じライフタイムであったとしても）。

したがって'``static``' ではない文字列を、上の例の ``Person`` に格納する場合は、次のいずれかの方法が必要になります：

1. ライフタイム中ずっとジェネリック型にする：

```Rust
struct Person<'a> {
    name: &'a str,
}

fn main() {
    let name = format!("fasterthanlime{}", "lime");
    let p = Person { name: &name };
    // `p` は `name` よりも長生きできません
}
```

あるいは

2. 文字列の所有権を受け入れる

```Rust
struct Person {
    name: String,
}

fn main() {
    let name = format!("fasterthanlime{}", "lime");
    let p = Person { name: name };
    // `name` は `p` に移動し、これら二つのライフタイムは全く別物になる
}
```

さらについて言えば：
構造体のメンバに同じ名前の型の変数が代入されている場合：

```Rust
    let p = Parson { name: name };
```

次のように短縮できます：

```Rust
    let p = Parson { name };
```

Rust が提供しているたくさんの型は「所有する」（*owned*）系と「所有しない参照型」（*non-owned*）系に分かれます：

- 文字列： ``String`` 型は「所有する」、``&str`` 型は「所有しない」です
- パス： ``PathBuf`` 型は「所有する」、``&Path`` 型は「所有しない」です
- コレクション： ``Vec<t>`` 型は「所有する」、``&[T]`` 型は「所有しない」です

Rust にはスライスもあります
ー スライスとは複数の連続した要素への参照です。

たとえばベクトル型のスライスを借用することが可能です：

```Rust
fn main() {
    let v = vec![1, 2, 3, 4, 5];
    let v2 = &v[2..4];
    println!("v2 = {:?}", v2);
}

// この出力は
// v2 = [3, 4]
```

上の例は魔法でもなんでもありません。
インデックス演算子（``foo[index]``）が ``Index`` と ``IndexMut`` の二つのトレイトでオーバーロードされているだけです。

キーワード ``..`` は単に「範囲」を示すリテラルです。
範囲は標準ライブラリで定義されている構造体の一部です。

範囲の前にキーワード ``=`` が付いている場合、スライスに記述した右端の境界（要素）を含まない要素の列を返します。

```Rust
fn main() {
    // 0 以上の数値？
    println!("{:?}", (0..).contains(&100));    // これは真
    // 厳密に 20 未満の数値？
    println!("{:?}", (..20).contains(&20));    // これは偽
    // 20 または 20未満の数値？
    println!("{:?}", (..=20).contains(&20));    // これは真
    // 3, 4, 5 だけ
    println!("{:?}", (3..6).contains(&4));    // これは真
}
```

借用のルールをスライスに適用すると：

```Rust
fn tail(s: &[u8]) -> &[u8] {
    &s[1..]
}

fn main() {
    let x = &[1, 2, 3, 4, 5];
    let y = tail(x);
    println!("y = {:?}", y);
}
```

上の処理は次と同じです：

```Rust
fn tail<'a>(s: &'a[u8]) -> &'a[u8] {
    &s[1..]
}
```

したがって、次のような使い方は正しいです：

```Rust
fn main() {
    let y = {
        let x = &[1, 2, 3, 4, 5];
        tail(x)
    };
    println!("y = {:?}", y);
}
```

...ただし、これが成立するのは``[1, 2, 3, 4, 5]`` が ``'static`` なライフタイムを持つ配列である場合だけです。

したがって、次のような使い方がは間違いです：

```Rust
fn main() {
    let y = {
        let v = vec![1, 2, 3, 4, 5];
        tail(&v)
        // error: `v` does not live long enough
    };
    println!("y = {:?}", y);
}
```

その理由は、配列 ``vec`` の値がヒープ領域に確保され、``'static`` ではないライフタイムを持っているからです。

``&str`` の値もスライスです。

```Rust
fn file_ext(name: &str) -> Option<&str> {
    // この関数は新しい文字列を生成しません
    // 受け取った引数のスライスを返します
    name.split(".").last()
}

fn main() {
    let name = "Read me. Or don't.txt";
    if let Some(ext) = file_ext(name) {
        println!("file extension: {}", ext);
    } else {
        println!("no file extension");
    }
}
```

...そして、ここにも借用ルールを適用すると：


```Rust
fn main() {
    let ext = {
        let name = String::from("Read me. Or don't.txt");
        file_ext(&name).unwrap_or("")
        // error: `name` does not live long enough
    }
    println!("extension: {:?}", ext);
}
```
