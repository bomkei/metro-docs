# 言語仕様

# 字句
- [トークン](./lexical/token.md)

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
## 演算子


## 変数定義

## 分岐文
### if
### switch

## 繰り返し文
### loop
### for
### while
### do-while




## 関数定義



## 列挙型 (enum)


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
- [組み込み関数](./builtin-functions.md)
