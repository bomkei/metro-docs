# 言語仕様

# 字句
トークンです。これ書く必要あるのかな？


# 組み込み型
### 数値型
- `int` <br> 符号付き 64 bit 整数

- `usize` <br> 符号なし 64 bit 整数<br>
    配列のインデックス参照、長さなど

- `float` <br> 浮動小数点数

### 真偽型
- `bool` 

### 文字、文字列
Metro の文字は UTF-16 で表現されます。
- `char`
- `string`

### その他
- `range` 範囲
- `vector` 可変長配列
- `dict` 辞書
- `none` 無し

# 式

## 演算子の優先順位
### 1
|  |  |
|--|--
| `( )` | 関数呼び出し

### 2
| | |
|---|--|
| `[ ]` | 配列作成
| `dict<T, U>(...)` | 型付き辞書
| `cast<T>(...)` | 型変換

### 3
| | |
|--|--|
|`[` `]` | 配列インデックス 
| `.` | メンバアクセス

### 4
| | |
|--|--|
| `+` | 単行プラス
| `-` | 単行マイナス

### 5
| | |
|--|--|
| `*` | 乗算
| `/` | 除法
| `%` | 剰余

### 6
| | |
|--|--|
| `+` | 加算
| `-` | 減算

### 7
| | |
|--|--|
| `<<` | 左シフト
| `>>` | 左シフト

### 8
| | |
|--|--|
| `==` | 等式
| `!=` | 不等式
| `>=` | 次の数以上
| `<=` | 次の数以下
|  `>` | 右より大きい
|  `<` | 右より小さい

### 9
| | |
|--|--|
| `&` | AND
| `^`  | XOR
| `\|` | OR

### 10
| | |
|--|--|
| `&&` | 論理積
| `\|\|` | 論理和

### 11
| | |
|--|--|
| `..` | 範囲

### 12
| | |
|--|--|
| `=` | 代入

# 構文

## スコープ
最後にセミコロンがない場合、最後の式がスコープの評価結果となる
```
scope       = "{" (expr semi?)* "}"
```
```
println({ 1; 2; 3 });  // => 3
```

## 変数定義

```
let         = "let" ident (":" typename)? ("=" expr)?
```
```
let a = 1 + 2;                 // int
let a = { a: to_string(a) };   // dict<int, string>
```

## 分岐文
### if
これがなきゃだめというやつですね。条件式はかっこで括る必要はありません。
```
if          = "if" expr scope ("else" (if | scope))?
```
```
fn func(n: int) -> int {
    if n == 10 {
        "ten"
    }
    else {
        "not ten"
    }
}
```

### switch
金欠でスイッチを売ったことがあります。
```
case        = "case" expr ":" scope
switch      = "switch" expr "{" case* "}"
```
```
let x = input();

switch x {
    case "aiueo": {
        println("あいうえお");
    }

    case x.startswith("abc"): {
        println("ABC" + x.substr(3));
    }
    
    default: {
        if x.length() == 5 {
            break;
        }

        println("x.length() != 5");
    }
}
```

## 繰り返し文
### `loop`
```
loop        = "loop" scope
```
```
loop {
    println("hellp");
}
```

### `for`
いまのところ範囲 for 文だけです<br>
イテレータで変数名を書くと for 文のスコープで自動定義されます。
（イテレータじゃないけど）
```
for         = "for" expr "in" expr scope
```
```
for i in 0 .. 10 {
    println(i);
}

// =>
//  0
//  1
 ...
//  9
```


### `while`
```
while       = "while" expr scope
```
```
let i = 0;

while i < 10 {
    println(i);
    i = i + 1;
}
```

### `do-while`
```
do_while        = "do" scope "while" expr
```
```
let i = 0;

do {
    println(i);
    i = i + 1;
} while i < 10;
```



## 関数定義
戻り地の指定が必要
```
argument    = ident ":" typename
function    = "fn" "(" argument ("," argument)* ")" "->" typename scope
```
```
fn fibo(n: int) -> int {
    if n < 2 {
        return 1;
    }

    fibo(n - 2) + fibo(n - 1)
}
```

## 列挙型 (enum)
値をもたせることができます
```
enumerator  = ident ("(" typename ")")?
enum        = "enum" ident "{" enumerator ("," enumerator)* "}"
```
```
enum Kind {
    A,
    B(int),
    C(string)
}

let kind : Kind = Kind.C("Abc0123");
```


## 構造体 (struct)
構造体がないのに静的型付けは名乗れない<br>
メンバ変数は 名前 : 型 と書きます
```EBNF
member      = ident ":" typename
struct      = "struct" ident "{" member ("," member)* "}"
```
```
struct MyStruct {
    num: int,
    str: string
}

let x : MyStruct;
println(x);  // => MyStruct{ num: 0, str: "" }
```

## `new`: 構造体初期化
任意の初期値を使用して、構造体を作成します。メンバ変数の省略は一切できず、また順番の前後もできません。<br>
このキーワードを使うとまるでガベージコレクタがメモリ管理をしてくれる言語みたいな雰囲気がしますね。
いや、実際そうなんですけどね。<br>

bnf:
```
init    = <name: ident> "=" <expr>
new     = "new" <typename> "{" <init> ("," <init>)* "}"
```

sample:
```
let x = new MyStruct { .num = 10, .str = "abc" };
```


## `impl`: メンバ関数を定義
`impl` を使って、自分で定義した構造体にメンバ関数を追加しましょう。
スコープ内には関数しか書けません。

第一引数に `self` と書くことで、インスタンスに対して呼べるようになります。<br>
書かない場合、静的メンバ関数になります。<br>


```
impl        = "impl" <typename> "{" function* "}"
```
```
impl MyStruct {
    // 静的メンバ関数
    fn new(n: int) -> MyStruct {
        new MyStruct { .num = n, .str = to_string(num) }
    }

    // 非静的メンバ関数
    fn to_str(self) -> int {
        self.num
    }
}

let x = MyStruct.new

println()
```

静的メンバ関数と同じ構文で、非性的メンバ関数を呼ぶことはできません。
```
println(MyStruct.to_str(x)); // => ERROR
```

そしてなんと、自分で定義した構造体だけじゃなくて、型であればなんだってメンバ関数を追加できちゃいます。（・・・という予定）<br>


## ファイル分割
`import` を使う　拡張子は書かなくていい<br>
実行した場所より上にいくのはまだできない<br>
`"import" ident ("/" ident)*`
```
// sub.metro
fn fibo(n: int) -> int { ... }

// main.metro
import sub

println(fibo(10))
```

# メモリ管理
疑似ガベージコレクタが自動で削除する。<br>
ガベージコレクタと言えるかどうかはさすがに微妙だけど。


## 組み込み関数
- [組み込み関数](builtin-functions.md)
