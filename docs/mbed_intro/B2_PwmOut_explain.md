[ホームに戻る](./B_index_lecture.md)  
# PwmOutでモーターを回してみよう
今回の記事では、DCモーターやサーボモータを回したりする際によく使う「**PWM出力**」の説明、およびそれを司る`PwmOut`クラスの説明をします。また、DCモーターを制御するのに不可欠な**モータードライバ**の説明もします。

## PWM出力とは
[第１話](DigitalOut_explain.md)で紹介したDigital出力は、3.3 Vか 0 Vかの２択しかありませんでした。もし出力電圧をある程度自由に変えることができれば、LEDの明るさを変化させたり、モーターの回転速度を変更させたりすることができます。しかし、マイコンの仕様上**ピンからは 3.3 Vと 0 V しか出力できません**。そこで、3.3 V と 0 Vを**高速で切り替えることで疑似的にその中間の値を再現する**のがPWM出力です。

## PWM出力の詳細

<div style="text-align: center;">
<image src = "./img/Pwm_ex.png" alt = "Prj_intro" title = "Prj_intro" width = "600" height = "200"/>
</div>

PWM出力について詳しく説明します。前述の通り、Pwm出力はHIGHとLOWを高速で切り替える出力です。このような非線形の波形を **パルス（波）** と呼びます。このパルス波の１周期について、その周期を**パルス周期**、HIGHである時間の長さを**パルス幅**、と呼びます。また、パルス周期に対するパルス幅の割合を**duty比**といいます。パフォーマンスが印加電圧に比例する素子（LEDとかDCモーターとか）では、当たり前ですが**duty比**がそのまま出力の大きさの目安になります。  
では次に、DCモーターを制御するための素子である**モータードライバ**について説明します。

## モータードライバとは
ご存じの通り、DCモーターは電源にさえつなげば回ります。しかしそのやり方では、いくつか問題があります。

* 正転・逆転・ブレーキ・自由回転　といったロボット制御に最低限必要な操作さえできない
* モーターから出るノイズ対策を自分で行わないといけない
* マイコンから出る電圧（3.3 V）は定格電圧に対して小さすぎることが多い。

このように電源やマイコンにDCモーターを直つなぎするやり方は安全かつ正確にDCモーターを制御するには心もとないです。そこで出てくるのが**モータードライバ**です。マイコンとモーターをモータードライバを介して接続することでこれらの問題を解決できます。
モータードライバは自作することもありますが、非常に難易度が高いので初心者の内は既製品を使うほうが良いでしょう。
では、実際にモータードライバ「TC78H653FTGモータドライバモジュール」を使って、DCモーターを制御してみましょう。

### モータードライバの仕様を把握しよう
モータードライバにはたくさんのピンがあり、どう使うのかを一目で理解するのは難しいです。そこで役に立つのが**データシート**（仕様書のようなもの）です。今回用いるものは購入元である**秋月電子通商**様のHPから確認することができます（リンクは[こちら](https://akizukidenshi.com/catalog/g/gK-14746/)）。  
これから様々なICやセンサを紹介しますが、たいてい販売サイトにデータシートが存在します。時には英語だったりしますが、使い方や注意点を把握するのに不可欠なので、**必ず読みましょう**。初めの内は要点さえ理解できれば大丈夫です。  

### データシートの読み方
モータードライバのデータシートで最低限読むべき箇所を説明します。タイトルはメーカーや製品によって若干異なります。

* 特長欄<br/>
その素子のスペックや制限が示されています。特に重要なのは「絶対最大定格」等の「定格」とついている数値です。これは**その素子が正常に動作する限界の値**が書いてあります。例えば最大定格以上の電圧をかけると、壊れます。最悪火花が飛んだり煙が出たりします（毎年誰かやらかします）。気を付けて回路を作れば特に問題ないです。

* 端子機能一覧<br/>
それぞれの端子の位置と役割が書いてあります。このモータドライバの素子は親切なので、基板の裏側に全部書いてありますね。「制御入力」は、マイコンが出力しモータードライバが読み取る端子で、「出力」はモーターと接続する端子です。

* 入出力ファンクション<br/>
入力端子の値によって、モーターがどのような挙動をするのかが表にまとめられています。今回はPhaze入力モードで動かします。

## 回路を組もう
ではさっそく回路を組みましょう。

### 用意するもの
| アイテム名 | 個数 |
| ---- | ---- | 
| Nucleo F303K8 | １つ |
| TC78H653FTG | １つ |
| DCモータ(FA-130) | １つ |
| 乾電池 | ４本 |
| 電池ボックス | １つ |
| ジャンパーピン | 適量 |

### 回路の様子
データシートをみて、回路を組みましょう。下記写真のようになるはずです。

<div style="text-align: center;">
<image src = "./img/Motor.png" alt = "motor_intro" title = "motor_intro" width = "600"/>
</div>

## プログラミングをしよう
回路が組み終わったら、プログラムを書きましょう。適当にプロジェクトを作ってください。

``` cpp
#include <mbed.h>

DigitalOut dir(PA_0);
PwmOut speed(PA_1);

int main() {
  speed.period_ms(20);
  speed.write(0);
  dir.write(1);
  // put your setup code here, to run once:

  while(1) {
    speed.write(0.5);
    dir.write(0);
    wait_us(1000000);

    speed.write(0);
    wait_us(1000000);
    
    speed.write(0.5);
    dir.write(1);
    wait_us(1000000);

    speed.write(0);
    wait_us(1000000);
    // put your main code here, to run repeatedly:
  }
}
```
プログラムが完成したら、書き込んでみましょう。正転→停止→逆転→静止を繰り返すはずです。

## 解説
PwmOutについて解説します。今回はモータードライバのPhazeモードを使ったので、マイコンからモータードライバに入力する信号は「向きを決める信号」と「回転かブレーキかを決める信号」の2種類です。後者をPwm出力にすることでスピードを調整します。


``` cpp
PwmOut speed(PA_0);
DigitalOut dir(PA_1);
```

例によって`PwmOut`クラスと`DigitalOut`クラスをインスタンス化させています。今までと同じですね。前述の通り、今回は二つの信号をそれぞれ「速度を指定する出力」「回転方向を指定する出力」として出しています。

``` cpp
speed.period_ms(20);
```
これは、periodの名の通りPwm波形のパルス周期（ミリ秒単位）を指定しています。これを指定しないとモーターが回りません。数字はモータードライバのデータシートを見て決めましょう。

``` cpp
speed.write(0);
dir.write(0);
```
各出力の初期値を定義しています（初期化といいます）。`while`文の処理が始まるまで止まっていてほしいので、速度を0にしています。
このように`インスタンス.write(duty比)`と書くことで指定したduty比で出力を行うことができます。なお、**モータードライバによってはduty比の最大値が100 %ではないことがあるので、注意してください**。  
パルス幅で出力を指定したい場合は、
``` cpp
speed.pulsewidth_ms(10);
```
のように書くこともできます。`インスタンス.pulsewidth_ms(パルス幅)`という形です。秒単位の`pulsewidh`、ミリ秒単位の`pulsewidth_ms`、マイクロ秒単位の`pulsewidth_us`があるので、適宜使い分けましょう。


## 練習問題１
DCモーターで加速と減速を10秒周期で繰り返すようなプログラムを作成してください。制御周期は1 msとします。
duty比の最大値は 50 %としてください。回転方向はお好きにどうぞ

<details><summary>解答例はこちら</summary>

``` cpp
#include <mbed.h>

DigitalOut dir(PA_0);
PwmOut speed(PA_1);
bool acc = true;//真なら加速　偽なら減速
float duty = 0;

int main() {
  speed.period_ms(20);
  speed.write(0);
  dir.write(1);
  
  // put your setup code here, to run once:

  while(1) {
    if(acc)
    {
      duty += 0.0001;
    }else
    {
      duty -= 0.0001;
    }
    if(duty > 0.5)
    {
      duty = 0.5;
      acc = !acc;
    }else if(duty < 0)
    {
      duty = 0;
      acc = !acc;
    }
    speed.write(duty);
    wait_us(1000);
    // put your main code here, to run repeatedly:
  }
}
```

加速するか減速するかを`bool`型を用いて判別しています。`write()`関数の引数は`float`型なので、float型の変数を渡すことで速度を変更できます。
</details>


# PWM出力でサーボモータを回してみよう
では続いて、DCモータと並びよく使われるサーボモータの使い方を解説します。

## サーボモータとは
サーボモータは、以下の写真のような形状のモータです。主に回転角や速度を正確に制御するために用いられます。ただ、高額なものを除いて、回転角が限られている（180° ~ 270°）ものがほとんどです。なので、ロボットの足回りというよりは、把持機構等で活躍します。

## サーボモータの使い方
サーボモータは、多くの場合3つのピンを回路に接続します。一つは電源入力、一つはGND、もう一つはPWM入力です。サーボモータは、 **PWM入力と回転角（回転速度ではない）が1対1対応します** 。  
入力するPWMのパルス周期は製品ごとに異なるので、**データシートを見て確認**しましょう。今回使うSG90（下写真）は、パルス周期が20 ms、パルス幅が 0.5 ms ~ 2.4 msですね（リンクは[こちら](https://akizukidenshi.com/catalog/g/gM-08761/)）。データシートにあるように、パルス幅が0.5 msの時 -90°、2.4 msの時 90°なので、これをもとにすれば指定した角度の位置に回転させることができるでしょう。中間の角度は普通に比例配分すれば大丈夫です。

<div style="text-align: center;">
<image src = "./img/SG90_pic.jpg" alt = "Prj_intro" title = "Prj_intro" width = "600" height = "200"/>
</div>

## 練習問題２
サーボモータを使って、ちょうど60°回転し元に戻る、という往復運動を1秒周期で繰り返すようなプログラムを作成してください。

<details><summary>解答例はこちら</summary>

``` cpp
#include <mbed.h>

PwmOut theta(PA_1);

int main()
{
  theta.period_ms(20);

  while(1)
  {
    theta.pulsewidth_ms(1.13);
    wait_us(500000);
    theta.pulsewidth_ms(0.5);
    wait_us(500000)
  }
}
```

普通に比例配分しましょう。あなたならできるはず。
</details>

## おまけ　サーボモータにモータードライバは要らないの？
ＤＣモーターの時とは違ってサーボモーターではモータードライバを使いませんでした。実はサーボモーターは**DCモーター**と**モータードライバ**（とギアと可変抵抗器）が内蔵されていて、それによってPWM信号を回転角に変換しています。なので、不要というよりは「すでに持っている」というわけです。

## 発展問題
pulse幅の代わりに角度を指定することでサーボモータを制御できるクラスを作りましょう。
データシートから得られる各種パラメータ（パルス周期、パルス幅の最大値・最小値）の違いに対応できるようにしてください。