[ホームに戻る](./B_index_lecture.md)  

今回は、スイッチ等に用いられている`DigitalIn`を使い方について解説します。その例として、「スイッチを押すとLEDが光る装置」を作ってみましょう。

# スイッチを含む回路を作ってみよう
プログラムを書く前に、回路を作ってみましょう。
## 用意するもの
| アイテム名 | 個数 |
| ---- | ---- | 
| Nucleo F303K8 | １つ |
| LED 好きな色 | １つ |
| 抵抗 200 Ω | １つ |
| 抵抗 10 kΩ | １つ |
| タクトスイッチ | １つ |
| ジャンパーピン | 適量 |

## 配線の様子
以下の画像を参考にしてください

<div style="text-align: center;">
<image src = "./img/kairo.jpg" alt = "Prj_intro" title = "Prj_intro" width = "400" height = "200"/>
</div>


## タクトスイッチに抵抗がついている理由
電子工作初心者の人には、タクトスイッチの配線がやや奇妙に見えるかもしれません（筆者は初めて見たときそう思いました）。タクトスイッチの配線についている抵抗は、「**プルアップ抵抗**」と呼ばれています。これの働きについて、もう一つの方式である**プルダウン抵抗**と併せて理解する必要があります。
外部サイトにとても詳しい説明があるので、今回はそれにお任せしようと思います。
  [リンクはこちら](https://voltechno.com/blog/pullup-pulldown/)

# DigitalInでスイッチを使ってみよう
適当な名前のプロジェクトを作り、以下のソースコードを打ち込んでください。プロジェクトの作り方は[前回](DigitalOut_explain.md)紹介済みなので、忘れた人は確認してください。

``` cpp
#include <mbed.h>

DigitalIn Sw1(PA_0);
DigitalOut myled(LED1);

int main() {
  // put your setup code here, to run once:
  while(1) {
    if(Sw1.read() == 0)
    {
      myled = 1;
    }else{
      myled = 0;
    }
    wait_us(1000);
    // put your main code here, to run repeatedly:
  }
}
```


さっそくマイコンにプログラムを書き込んで実行しましょう。正常に動作していれば、ボタンを押している間だけLEDが光るはずです。

<div style="text-align: center;">
<image src = "./img/testrun1.GIF" alt = "Prj_intro" title = "Prj_intro" width = "400" height = "300"/>
</div>

  
## 解説：DigitalIn
前回と同じようにソースコードの解説をしていきます。前回説明した分は飛ばします。

``` cpp
DigitalIn Sw1(PA_1);
```
このコードで`DigitalIn`のインスタンスを作成しています。書き方は`DigitalOut`と同じです。
`DigitalIn`は**デジタル入力**を司るクラスで、これを用いることで**そのピンに電圧がかかっているか否かを判定**できます[^1]。

``` cpp
if(Sw1.read() == 0) // if(!Sw1.read()){...}　でも同様
{
    ...
}
```

`DigitalIn`のメンバ関数である`read()`は、そのピンに電圧がかかっている（= HIGH）と`1`を、かかっていない（= LOW）と`0`を返します。プルアップ抵抗の回路では、スイッチが押されると電圧が0になり、押されていないとき3.3 Vになります。ということで、これによって「スイッチを押しているときに `｛｝` 内を処理する」というプログラムになっています。1と0はそれぞれ真・偽を兼ねるので、コメントアウト以降のような略記も可能です。

``` cpp
while(1)
{
    ...
    wait_us(1000);
}
```
while文の最後に`wait_us`を入れてあります。これによって、1 msに一回スイッチが押されているか判定&処理を行うようにしています。このような処理を行う周期のことを**制御周期**といいます[^2]。一般に制御周期は速いほど良いですが、速すぎると処理が追い付かなくなるので、そのプログラムごとに良い塩梅を見つけていくことになります。

## 練習問題
LEDが普段ついていて、ボタンを押している間だけLEDが消えるプログラムを作ってください。

<details>
<summary>解答例はこちら</summary>

``` cpp
#include <mbed.h>

DigitalIn Sw1(PA_0);
DigitalOut myled(PB_1);

int main() {
  // put your setup code here, to run once:
  while(1) {
    if(Sw1.read())
    {
      myled = 1;
    }else{
      myled = 0;
    }
    wait_us(1000);
    // put your main code here, to run repeatedly:
  }
}
```

先ほどの例の`if`文の真偽を反転させれば良いでしょう。

</details>

　　
## 発展問題
ボタンを押すとLEDが 1 秒周期で点滅を開始するようなプログラムを作ってください。

<details><summary>解答例はこちら</summary>

``` cpp
#include <mbed.h>

DigitalIn Sw1(PA_0);
DigitalOut myled(LED1);

int main() {
  // put your setup code here, to run once:
  myled = 0;
  while(Sw1.read()){};

  while(1) {
    myled = 1;
    wait_us(500000);
    myled = 0;
    wait_us(500000);
    // put your main code here, to run repeatedly:
  }
}
```

「スイッチが押されていない」という条件での無限ループを点滅の処理の前にはさめば、スイッチが押されるまで待機させることができます。

</details>
  

←　[第１話](DigitalOut_explain.md)　｜　[第3話](PwmOut_explain.md)　→

[^1]: 具体的に何ボルトかかっているかはDigitalInでは分かりません。
[^2]: 逆数を取って制御周波数（Hz）として考える場合もあります。