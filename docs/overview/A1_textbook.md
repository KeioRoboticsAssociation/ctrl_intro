# A1. C++の教科書の選び方 / プログラミングの勉強の仕方
[00.制御班の概要](./00_overview.md)で述べたように、
このサークルでは**主にC++を用いて**プログラムを書きます。
C++の教材はこのページ群にも載せますが、**C++の教科書を持っておく**のもよいと思います。
というのも、ロ技研で用意しているC++の教材は**ロボット制御で使うことを念頭に置いている**ためです。C++のみの基本講座は筆者の[友人の記事](https://hirobon1690.github.io/intro-to-cpp/)が有用なのでこれを使ってください。
そのため、C++の汎用機能をくまなく紹介するものではありません。
勉強する以上はほかの用途でもプログラミングを使えた方が面白いでしょうから、
本は１冊持っておくことをお勧めします（強制ではないです）。

# おすすめ教科書
下の表に良く知られているC++の教科書を示します。
なお、オススメ度などは何人かの部員の私見をもとに決めています。
しっかり選ぶなら、一度**試し読みしてみることをお勧めします**。

|本の名前|分かりやすさ| 分量 | 内容充実度 | オススメ度 | 一言コメント|
|:-----:|:----:|:-----:|:----:|:----:|:------|
|[C++の絵本](https://onl.sc/6is3GJc)|★★★★|少なめ| ★★☆☆ |★★★★|とてもやさしい書き方で基礎を網羅してる|
|[猫でもわかるC++](https://onl.sc/LTKcwQZ)|★★★★|普通| ★★★☆ |★★★★|易しい書き方で基礎+αがわかる|
|[独習C++](https://onl.sc/tqTvUKV)|★★☆☆|普通| ★★★☆ |★★★☆|他言語が使える人向け|
|[C++プログラミング入門](https://onl.sc/bqNyb9R)|★★★☆|普通| ★★★☆ |★★☆☆|他言語が使える人向け|
|[ローベルのC++入門](https://onl.sc/Vh9riht)|★☆☆☆|凄く多い| ★★★★ |★☆☆☆|辞書としてはアリ|

# プログラミングの勉強の仕方
※説教臭い話が嫌いな人は読み飛ばしてください<br>
プログラミングの勉強の仕方は、**学校でやる勉強のやり方とは異なります**。<br>
大事なことは、以下の３つです。<br>

1. とにかく自分でタイピングして実装する
2. 積極的に自分で調べて、人に聞く
3. エラーメッセージはお友達

順に説明します。
## 1.とにかく自分でタイピングして実装する
とにかくプログラムを**書いて書いて書きまくる**ことがプログラミング上達の近道です。たまに教科書に書いてあるコードを**ノートに書いて整理している人がいますが、彼らが上達したところを見たことがないです**。<br>
英語を**書いたり喋ったり**することで習得する動作は、プログラミングにおいては**タイピングして実行する**ことに相当します。<br>
このサイトでは毎記事サンプルプログラムと練習問題を掲載する予定ですが、物足りない人は**競技プログラミング**に手を出すのもありだと思います。
競技プログラミングで求められる技術とロボットへのプログラムで求められる技術は微妙に異なりますが、**効率的なプログラムの組み方**が身についたり、**豊富な実装経験**が積めたりするので意義はあると思います。
[Atcoder](https://atcoder.jp/?lang=ja)などは日本語の練習問題・解説がしっかりしているのでおすすめです。<br>

## 2.エラーメッセージはお友達
書いたプログラムを実行すると、基本的に**一発目は動きません**。大抵の場合どこかに書き間違え・処理の間違いなどがあります（ロボットを動かす長大なプログラムでは尚更です）。
プログラミングに関しては、入試のように**ミスなく一発でプログラムを仕上げるより、ミスを毎度修正するほうが良い**です。<br>
そして、<font color="Red">赤字</font>などでエラーメッセージが表示されます。はじめのうちはビビってしまう人が多いようですが、よく読むと**どこにエラーがあるのか懇切丁寧に書いてある場合が多いです**。特に"<font color="Red">Error: ~</font>"と書いてある場所は要チェックです。

## 3.積極的に自分で調べて、人に聞く
ロ技研は部員の自主性に重きをおいているので、**情報とタスクは自分で取りに行かないと得られません**。わからないことがある場合は、まず調べてみましょう。特に新しい機能を使うときや、エラーで動かない場合は、**一人で悩んでも解決しないことが多い**ので、ためらわずGoogle先生の力を借りましょう。<br>
ネット上には自分と似たような失敗をした先人たちが解決法をブログにしてくれているので、彼らの力を頼るべきです。<br>
~~怪しい科学・健康系の記事とは異なり~~、プログラミングについて嘘を書いてもいいことないので、信憑性はそこそこ高いです。
