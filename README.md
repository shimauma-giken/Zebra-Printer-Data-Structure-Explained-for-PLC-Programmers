# Zebra-Printer-Data Structure Explained for PLC Programmers
 PLC開発者必見。ゼブラプリンタの電文構造の説明。


# RS-232C インターフェイス仕様
　
<br>


| 名称 | 設定 |
|:-:|:-:|
| ボーレート     | 2400 ～ 115000 / Default 9600 
| データ          | 8 bit
| パリティ        | none
| ストップビット      | 1 bit
- XON-XOFF、RTS/CTS、または DTR/DSR ハンドシェイク・プロトコルが必要
- 5 V で 750 mA ( ピン 1 ～ 9)

<br>

RS-232Cについてはユーザーズ・ガイド等の資料をご参照ください。

ZT610 Users Guide  
https://www.zebra.com/content/dam/zebra_new_ia/en-us/manuals/printers/industrial/zt600/zt600-ug-en.pdf


<br>
<br>



# 電文仕様

Zebra プリンタは送信コマンド方式によって、電文フォーマットが異なる。本書では数多くあるコマンド形式の中で最も利用されるZPLとSGDについて記載をする。

<br>

具体的なプリンタコマンドや詳細は下記プログラミングガイドを参照すること。

### ZPL II, ZBI 2, Set-Get-Do, Mirror, WML Programming Guide （英語）
https://docs.zebra.com/content/tcm/us/en/printers/software/zebra-zpl-ii,-zbi-2,-set-get-do,-mirror,-wml-programming-guide/set-go-do-programming-reference-guide.html

<br>
<br>

## # ZPL Format

<br>
ZPLの最小構成は下記の通りとなる。下記構文とは異なる構文をとるコマンドも存在するため、具体的なプリンタコマンドや詳細はプログラミングガイドを参照すること。

<br>

| 名称 | 構文 | 使用例 | 解説 |
|-|-|-|-|
| 順次実行コマンド  | ^XXx,xx,xx.... | ^XA^FO50,50^XZ   | FIFOで実行。実行時はヘッダ（^XA）とフッター（^XZ）が必要。
| 即実行コマンド    | ~XX            | ~HS          | 割り込み処理を実行。

- X (大文字) --- コマンド部　　
- x (小文字) --- パラメータ部

<br>
<br>


### 送信データ
| STX | Command Data | ETX |
|-|-|-|

<br>

    例、印刷
    --------------------------------------
    送-> <STX>^XA ^FO50,50 ^A0N,32,32 ^FDZebra Zebra Zebra^FS ^XZ<ETX>


<br>

### 受信データ
\* 受信データがある場合

| STX | Recieve Data | ETX | CR | LF |
|-|-|-|-|-|

    例、Host Status の取得
    --------------------------------------
    送-> <STX>~HS<ETX>
    ->受 <STX>159,0,0,0417,000,0,0,1,000,0,0,0<ETX><CR><LF><STX>000,0,0,0,0,2,4,0,00000000,1,000<ETX><CR><LF><STX>0000,0<ETX><CR><LF>


<br><br>

## # (SGD) Set-Get-Do Format

<br>

| 名称 | 構文 | 使用例 | 解説 |
|-|-|-|-|
| 設定コマンド  | ! U1 setvar "[パラメータ]" "[設定値]" | ! U1 setvar "bluetooth.enable" "on"   | プリンタのパラメータを設定する。
| 取得コマンド  | ! U1 getvar "[パラメータ]"  | ! U1 getvar "bluetooth.enable"   | プリンタのパラメータを取得する。

- [パラメータ] --- 指定対象のパラメータ
- [設定値] --- パラメータに設定する値

<br>

### 送信データ
| STX | Command Data | CR | LF 
|-|-|-|-|

    例、Bluetoothの有効化設定の変更
    --------------------------------------
    送-> <STX>! U1 setvar "bluetooth.enable" "off"<CR><LF>

<br>

### 受信データ
\* 受信データがある場合

| Recieve Data | 
|-|

    例、Bluetoothの有効化設定の確認
    --------------------------------------
    送-> <STX>! U1 getvar "bluetooth.enable"<CR><LF>
    ->受 "off"


<br>

