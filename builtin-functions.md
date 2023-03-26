# 組み込み関数
コードブロックのやつは疑似コードです

## `input`
入力を受け付ける
```
fn input() -> string
```

## `print`
与えられた引数を全てコンソールに出力します。
```
fn print(...) -> int
```

## `println`
print に改行を足しただけ

## `length`
文字列とか配列とかの要素数を取得する
```
fn length(x: string) -> usize
fn length <T> (x: vector<T>) -> usize
```

## `id`
オブジェクトの識別 ID です。いまのとこ使いみちなし。