# 言語仕様

# 字句
トークンです。これ書く必要あるのかな？


# 組み込み型
| 名前 | 説明 | 用途 | 値 |
|------|------|----|----|
| `int`   | 符号付き 64 bit 整数 | 
| `usize` | 符号なし 64 bit 整数 | 配列のインデックス参照、長さなど
| `float` | 倍精度浮動小数点数 |
| `bool`  | 真偽型   |   | `true` `false`
| `char`  | UTF-16 文字 |      | 0 ~ 65535
| `string` | UTF-16 文字列 |  |
| `range` | 範囲 |  |
| `vector` | 可変長配列
| `dict` | 辞書



# 構文
便宜上、if とか for の制御文をステートメントっていいますが、全部式です。<br>
適切な言い方はなんだろう。

## 式
式としかいいようがない

## 演算子
演算子としか言いようがない　ドキュメントなんてろくに書いたことない<br>
とりあえずせめて優先順位だけでも書いておく
|  |  |
|---|----
|1 | `(` `)` 関数呼び出し
|2 | `cast` 型変換
|3 | `[` `]` 配列インデックス `.`メンバアクセス
|4 | `+` 単行プラス `-` 単行マイナス
|5 | `*` `/` `%` 掛け割り余り
|6 | `+` `-` 足し引き
|7 | `<<` `>>` シフト
|8 | `==` `!=` `>=` `<=` `>` `<` 比較
|9 | `&` `^` `\|` ビット演算
|10 | `&&` `\|\|` 論理積、論理和
|11 | `..` 範囲
|12 | `=` 代入

## 変数定義
変数を定義するだけ　ちなみにシャドウイングできます
```
let         = "let" ident (":" typename)? ("=" expr)?
```
```
let a = 1 + 2;                 // int
let a = { a: to_string(a) };   // dict<int, string>
```

## スコープ
最後にセミコロンがない場合、最後の式がスコープの評価結果となる
```
scope       = "{" (expr semi?)* "}"
```
```
println({ 1; 2; 3 });  // => 3
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

## new
メンバの初期値を指定して構造体を作成します。絶対に全部のメンバの初期値を書かせます。<br>
このキーワードを使うとまるでガベージコレクタがメモリ管理をしてくれる言語みたいな雰囲気がしますね。
いや、実際そうなんですけどね。<br>

初期化子の構文は C++ と同じです
```
init        = "." ident "=" expr
new         = "new" typename "{" init ("," init)* "}"
```
```
let x = new MyStruct { .num = 10, .str = "abc" };
```


## メンバ関数 (impl)
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
