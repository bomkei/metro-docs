# Metro Documents

[[English]](./README-en.md)
[[Japanese]](./README.md)

2021-2023 (C) Letz <br>
英語に翻訳してくれる方を募集しています！

## Links
- [Metro](https://github.com/bomkei/metro)
- [Discord](https://discord.gg/pWNv7PPryp)
- [Twitter](https://twitter.com/metro_lang)
- [開発者 Twitter](https://twitter.com/letz00)
- [開発者 GitHub](https://github.com/bomkei)

## Special Thanks

- [Bamboo](https://github.com/HIDE0123) <br>
デバッグ、ドキュメント作成の手伝い、その他いろいろ

# これはなに
静的型付けスクリプト言語「Metro」の公式ドキュメントです。

# Metro とは
C++ で開発されている 静的型付けスクリプト言語 です。(リポジトリは[ここ](https://github.com/bomkei/metro)) <br>
Rust のような構文が特徴で、型システムについては C# や C++ を参考にした独自の設計がされています。 <br>

## 言語を使ってみる
リポジトリをクローンしてビルドし、`Hello, World!` を出力させる手順。
```
git clone https://github.com/bomkei/metro.git
cd metro
make -j
echo "println(\"Hello, World!\");" > test.metro
./metro test.metro;
```

# 仕様書
### [言語仕様](./papers/ja/lang/)
### [インタプリタ](./papers/ja/interpreter/)
