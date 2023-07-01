[ホームに戻る](./index.md)  
←　[第３話](PwmOut_explain.md)　｜　[第５話](Warikomi_explain.md)　→

# AnalogInでセンサーを使ってみよう。
この記事では、センサーの読み取りなどで用いるAnalogInについて、例を交えてその使い方を説明します。

## Analog入力とは
Analog入力とは、連続的な信号（アナログ信号）を受信するものです。以前紹介したDigitalInは、電圧がHigh(3.3 V)かLow(0 V)かの2択しかありませんでしたが、AnalogInはピンにかかっている電圧を読み取ることができます。多くのセンサーは検知するものの度合いに応じて電圧が変化する仕様なので、センサーとAnalogInのピンを繋げることで、マイコンがセンサーの値を読み取ることができます。

## フォトリフレクターを使ってみよう
この記事では、センサーの例としてフォトリフレクタ（下画像）を用います。フォトリフレクターは赤外線の反射光の強度を計測するセンサーで、ライントレースカーや明るさの検知等に用いられます。今回はフォトリフレクタで読み取った値をPC上で表示させるプログラムを作ってみましょう。
今回使うフォトリフレクタのデータシートは[こちら](https://akizukidenshi.com/catalog/g/gP-04500/)。

## 回路を組もう
下画像のように回路を組んでください。

## シリアル通信を準備しよう
　今回、センサーで読み取った値をPC上で表示させるには、マイコンで処理した内容をPCに送信する必要があります。すなわち、 **マイコン・PC間で通信を行う必要があります** 。マイコン・PC間通信では主にSerial通信（UART）を利用します。初心者のうちはあまり深く知らなくても良いですが、そのうち重要になるので、詳しく知りたい人は各自調べてみてください（「Serial通信とは」とかで検索すれば見つかると思います）。

### 必要なソフトウェアを用意しよう
　パソコンのUSB端子とマイコンをつないで通信するのですが、そのためのソフトウェアを予めPC側に入れておく必要があります。Windowsを使っている方は、「Teraterm」というオープンソースのソフトウェアを使いましょう。非Windowsの人は部員に相談してください。
 [こちら]（https://osdn.net/projects/ttssh2/releases/）のリンクから `teraterm-〇〇.exe` というファイルをクリックしてダウンロードしましょう。諸々を許可してインストールください。設定は（特にこだわりなければ）デフォルトでOKです。
 日本人が開発したソフトなので日本語対応してます（神）。

## プログラムを作成しよう
下のようなプログラムを作成しましょう。

``` cpp
#include<mbed.h>

AnalogIn PhRe(PA_0);
asm(".global _printf_float");

int main()
{
    while(1)
    {
        printf("float:%f, u_int16:%f\n",PhRe.read(),PhRe.read_u16());
        wait_us(100000);
    }
    return 0;
}
```

完成したら、さっそく書き込んでみましょう。

### Teratermを通してセンサーの値を読もう
マイコンとPCをつないだ状態で、以下の操作をしてください。

* Teratermを起動する
* 「シリアル」の方へチェックを入れる
* ドライバを設定する



二つの数字が次々と表示されたら成功です。
その状態のまま、黒いところや白いところにセンサーを近づけてみましょう。数字が増減するはずです。

# 解説

``` cpp
AnalogIn PhRe(PA_0);
```
相変わらずのインスタンス化です。説明は省略

``` cpp
asm(".global _printf_float");
```
mbedでは、なぜか`printf("%f",hoge)`がうまく表示されません。それを解消するためのコードです。

``` cpp
PhRe.read();
```
`DigitalIn`と同じく`インスタンス名.read()` で電圧をfloat型として読むことができます。ただし、0 V ～ 3.3 V を0から1までのスケールに変換して読み込んでいるので、**電圧の値そのものを読んでいるわけではありません**。

``` cpp
PhRe.read_u16();
```
このように書くことで、電圧を0 ～ 1スケールではなく 0 ～ 65535 スケールの整数として読むことができます。
float型が扱いにくいと思った方はこちらを使ってもよいでしょう。


## 練習問題
フォトリフレクタとモーターを動かすのに必要なものを用意して、明るいところではモーターが回り、暗いところではモーターが静止するような装置およびプログラムを作成してください。

<details><summary>解答例はこちら</summary>

ソースコードは以下のようなものが考えられます。

``` cpp
#include <mbed.h>

DigitalOut dir();
PwmOut speed();
AnalogIn PhRe();

int main()
{
    dir.write(1);
    speed.period_ms(20);
    speed.write(0);

    while(1)
    {
        if(PhRe.read() > 0.5)
        {
            speed.write(0.5);
        }else{
            speed.write(0);
        }
        wait_us(100000);
    }
}
```
</details>