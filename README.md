# SoundDropMiniUNIT

<img src="https://github.com/akita11/SoundDropMiniUNIT/blob/main/SoundDropMiniUNIT.jpg" width="240px">

音声再生IC CH7800を使用した音声再生モジュールです。300KBのフラッシュメモリを搭載しており、PCにUSB Type-Cケーブルで接続するとUSBメモリとして認識され、そこにMP3音声データ等をコピーします。その音声データをGrove端子のシリアル(UART)通信で再生制御できます。UIFlow(v1)用のブロックもあります。

※CH7800に関する情報はWebページ等で検索してください。要点は以下にまとめてあります。


## 使い方（接続方法）

以下のようにスピーカやアンプを接続し、Grove端子からの制御で音声を再生します。
あわせて後述のcofig.txtでスピーカ出力（5文字目を"1"とする）に設定します（初期設定状態）。

### アンプ付きスピーカに接続する場合

3.5mmジャック（ステレオ：上部中央）に3.5mmステレオケーブル等でアンプに接続します。
あわせて後述のcofig.txtでDAC出力（5文字目を"0"とする）に設定します。


### スピーカを直接接続する場合

<img src="https://github.com/akita11/SoundDropMiniUNIT/blob/main/SoundDropMiniUNIT_spk.jpg" width="240px">

基板上部にある1.25mmピッチコネクタ(Molex 53047-0210用)をとりつける端子があり、市販の小型スピーカ（M5Stack本体に使われているものなど。AliExpressやTaobaoでも同等品があります）を接続することができます。※隙間がせまく差し込みにくいので注意してください。
あわせて後述のcofig.txtでスピーカ出力（5文字目を"1"とする）に設定します（初期設定状態）。


### 3.5mmジャックに直接スピーカを接続する方法

3.5mmジャックに直接スピーカを接続することもできます。その場合は、基板裏面のハンダジャンパを以下のように修正してください。（スピーカはモノラル再生となります）

<img src="https://github.com/akita11/SoundDropMiniUNIT/blob/main/SoundDropMiniUNIT_back.jpg" width="240px">

- JP2をカット
- JP3・JP4の中央と△マーク側をカットし、中央と反対側（△マークがない側）をショート


## 本体機能の設定

PC接続時に現れるドライブ内の"config.txt"で、以下の機能を指定できます。
設定情報は、config.txtの1行目に、以下の7桁の数値を記載します（それ以降は無視される）。
- 1文字目: ボタンの動作。0=再生中に中断可、1=再生中に中断不可、2=リピート再生1、3=リピート再生2、4=ON/OFF、5=常時リピート再生
- 2-3文字目: 音量(00-30)
- 4文字目: BUSY端子の機能(1=アクティブL、0=アクティブH)
- 5文字目: 出力(1=PWM(スピーカ)、0=DAC)
- 6文字目: ボタンの信号極性(0=アクティブL、1=アクティブH)
- 7文字目: 0=通常動作、1=低電力モード 

初期設定として"0300100"（音量30、スピーカ出力、など）となっています。


## 使い方（ソフトウエア）

あらかじめ、本体基板の一部にあるUSB Type-CにUSB Type-Cケーブルを使ってPCへ接続し、現れるドライブに使用する音声データ（*.mp3）をコピーしておきます。なおファイル名は、「"001"から"999"の3桁の数値」のみが有効（CH7800の仕様）となり、その数値が「曲番号」となります。


### UIFlow(v1)での使い方

「SoundDropMini.m5b」をダウンロードし、UIFlow(v1)の"Custom"の"Open *.m5b"からこのファイルを指定すると、Init、Play、Stopの3つのブロックを使用できます。

<img src="https://github.com/akita11/SoundDropMiniUNIT/blob/main/SoundDropMini_Block.png" width="240px">

- SoundDropMini_Init : 初期化。最初に1回だけ使用します。"TX"と"RX"には、使用するGroveポート・マイコン本体にあわせて、使用するピン番号を指定します。例えば、M5StackBasicのPortA（本体の赤いコネクタ）の場合は、上図のようにTX=21、RX=22を指定します。M5StackCore2のPortAの場合は、TX=32、RX=33となります。
- SoundDropMini_Play : numに指定した「曲番号」の音声を再生します。
- SoundDropMini_Stop : 再生中の音声を停止します。


### その他のソフトウエアでの使い方

搭載されている音声再生IC(CH7800)のデータシート等を参考に、制御コマンドをUART経由で送信して使用してください。通信プロトコルは「9600bps/N81」です。
制御コマンドの一部を以下にまとめます。

- 次の曲の再生: 0x7e 0x11 0x00 0x02 0x00 0x00 0xef
- 前の曲の再生: 0x7e 0x12 0x00 0x02 0x00 0x00 0xef
- 指定した曲(HH:LL＝曲番号(上位/下位の順))の再生: 0x7e 0x13 0x00 0x02 HH LL 0xef
- 音量UP: 7e 0x14 0x00 0x02 0x00 0x00 0xef
- 音量DOWN: 0x7e 0x15 0x00 0x02 0x00 0x00 0xef
- 音量をNNに設定: 0x7e 0x16 0x00 0x02 0x00 NN 0xef
- 再生停止: 0x7e 0x26 0x00 0x02 0x00 0x00 0xef



## Author

Junichi Akita (@akita11) / akita@ifdl.jp
