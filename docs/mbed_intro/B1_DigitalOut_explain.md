[ホームに戻る](./B_index_lecture.md) 

# プロジェクトを作ろう/DigitalOutでLチカしよう

[前回の記事](./intro.md)で環境構築が終わったので、さっそくプロジェクトを作ってプログラミングをしてみましょう。今回はマイコンとしてNucleo F303K8を使用します。

## プロジェクトの作り方
1. 画面左側のツールバーのPlatformIOのアイコンを押す。
2. `PIO Home`下の`Open`を押す。
3. `New Project`を押す。
<div style="text-align: center;">
<image src = "./img/Prj_intro1.png" alt = "Prj_intro" title = "Prj_intro" width = "400" height = "200"/>
</div>

4. `Name`に適当なプロジェクト名（**必ず英数字**）を入力する。
5. `Board`に使用するマイコンボードを入力する。"F303"と打ち込むと、"ST Nucleo F303K8"とサジェストが出るのでそれを選ぶ。
6. `Framework`には"mbed"を入力する。Arduinoで作りたくなったら"Arduino"と入力すればよい（今回はmbedを解説する）
<div style="text-align: center;">
<image src = "./img/Prj_intro2.png" alt = "Prj_intro" title = "Prj_intro" width = "400" height = "200"/>
</div>

7. `Location`を設定する。デフォルトは**非推奨**[^1]。デスクトップに適当なフォルダを自作してそこにプロジェクトを作りましょう。
<div style="text-align: center;">
<image src = "./img/Prj_intro3.png" alt = "Prj_intro" title = "Prj_intro" width = "400" height = "200"/>
</div>

8. 全部の設定が終わったら、`Finish`を押してプロジェクトを作りましょう。そこそこ時間がかかります。
9. 完了すると下の画像のような画面が表示されます。
<div style="text-align: center;">
<image src = "./img/Prj_intro4.png" alt = "Prj_intro" title = "Prj_intro" width = "400" height = "200"/>
</div>

## DigitalOutでLEDを点灯させよう
　一番初めのプログラムとして、マイコン上に搭載されているLEDを光らせてみましょう。
以下のようなプログラムを作成します。
``` cpp
#include <mbed.h>

DigitalOut myled(LED1);

int main() {
  // put your setup code here, to run once:
  myled.write(1);
  wait_us(1000000);
  myled.write(0);
  while(1) {
    // put your main code here, to run repeatedly:
  
  }
}
```
出来上がったら、ビルドして書き込みましょう。
下の画像のようにUSB経由でPCにつなぎます。
<div style="text-align: center;">
<image src = "./img/DO_img1.JPG" alt = "DO_img1" title = "DO_img1" width = "100" height = "150"/>
</div>
ビルドボタンと書き込みボタンは下画像の位置にあります。
<div style="text-align: center;">
<image src = "./img/kakikomi.png" alt = "Prj_intro" title = "Prj_intro" width = "400" height = "270"/>
</div>

プログラムが正常に実行されると、下の画像のように緑色のLEDが1 秒間点灯するはずです。
<div style="text-align: center;">
<image src = "./img/DO_img2.JPG" alt = "DOj_img2" title = "DOj_img2" width = "100" height = "150"/>
</div>

## 解説：DigitalOut
先ほどのコードを一行ずつ解説していきます。
### include文
``` cpp
#include <mbed.h>
```
mbedのライブラリを使えるようにしています。
**ライブラリとは、便利な機能（プログラム）の集まりのようなもの**です。mbed.hをincludeすることで、マイコンプログラミングに必要となる機能を使えるようになります。<br>
C++にはこのようなライブラリ（画像処理・数学・文字列処理 etc）が膨大に存在しているので、**必要なものだけを"#include<ライブラリ名>"で読み込むことにしています**。


### DigitalOut
``` cpp
DigitalOut myled(LED1);
```
"mbed.h"の機能の一つである**DigitalOut**を使うための準備です。(クラスを勉強済みの方向けに言うと、DigitalOut クラスのインスタンスを作成しています。)

```
DigitalOut　自分で決めた名前（ピンの名前）
```
の形式で宣言します。ピンの名前は、[マイコンのピン配置の図](https://os.mbed.com/platforms/ST-Nucleo-F303K8/)を参考にしてください。`PX_N`(`PA_6`等)と書いてある水色の四角に囲まれたものがピンの名前です[^3]。

DigitalOutは、名前の通り**デジタル出力を司る**機能です。要するに、**名前で指定したピンから電圧(3.3 V)をかけるか、かけない(0 V)かを管理している**だと思ってください。これによってLEDを点滅させたり、ブザーを鳴らしたりできます。

今回のコードでは、「マイコン上のLED1にデジタル出力をする」ための「myLED」という名前のものを用意しました。


### デジタル出力
``` cpp
myled.write(1);
```
このコードで先ほど指定したピンへ電圧をかけます。マイコン内部でピンと3.3 Vの線が接続され、電気が流れる仕組みです。逆に
``` cpp
myled.write(0);
```
でピンとGNDが接続され、電圧をかけない状態にすることもできます。

また、プログラムにおいては、数字の1と0は特別な意味を持ち、下の表のようにいろいろな値と対応します。
| |1|0|
|---|---|---|
|真偽値|真(true)|偽(false)|
|電圧|HIGH(3.3 V)|LOW(0 V)|

特に**true/falseと1/0は相互に書き換え可能**なので、是非覚えておいてください<br>

ちなみに、
``` cpp
myled = 1;
```
``` cpp
myled = 0;
```
と書いても正常に動作します。なぜOKなのかは発展的な内容なのでこの記事では扱いません。気になる人は「オペレーター指定子」で検索してください。

### 待機
``` cpp
wait_us(1000000);
```
この行で一定時間経つまで次の処理への移行を待つことができます。`wait_us`のusはマイクロ秒の意味です。このコードによって、LEDが点灯した後、その下のLED消灯のコードの処理に移るのを1秒間遅らせることができます。<br/>
ちなみに、ナノ秒単位で指定できる`wait_ns(ナノ秒)`もあります。**ミリ秒単位で指定できる関数はmbedOS6以降消滅したので存在しません**。欲しくなったら自作してください。

## 練習問題
先ほどのプログラムを改造して、マイコン上のLEDが1 秒周期で点滅するプログラムを作成してください。

<details><summary>解答例はこちら</summary>

``` cpp
#include <mbed.h>

DigitalOut myled(LED1);

int main() {
  // put your setup code here, to run once:
  while(1) {
    // put your main code here, to run repeatedly:
    myled.write(1);
    wait_us(500000);
    myled.write(0);
    wait_us(500000);    
  }
}
```

点滅のような繰り返しのある処理は、**ループ処理**を使えばいいのでしたね。今回は無限に点滅させたいので、`while`文を使うと手っ取り早いです。点滅周期が1 秒なので、0.5 秒毎に点灯と消灯を交互に繰り返せばよいでしょう。

</details>

以上、DigitalOutについて要点を説明しました。さらに詳しく知りたい人は[こちら](https://os.mbed.com/docs/mbed-os/v6.15/apis/digitalout.html)からリファレンスマニュアル[^2]を読んでください。

[^1]: PlatformIOは**日本語を含むアドレスにアクセスできない**ので、"C:\Users\username\Onedrive\ドキュメント"みたいな場所にファイルを作るとうまく実行できない場合があります。
[^2]: リファレンスマニュアル（または「リファレンス」）とは、そのクラスの情報を纏めたもので、要するに辞書のようなものです。mbedには英語版（公式）と日本語版（非公式）があります。
[^3]: マイコンボード上のLEDには、"LED1"等がピン名の代わりに割り振られているので、今回の例ではそれを用いています。

