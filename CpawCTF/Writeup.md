# Writeup

## ジャンル：[Crypto]
### Classcal Cipher

```
暗号には大きく分けて、古典暗号と現代暗号の2種類があります。
特に古典暗号では、古代ローマの軍事的指導者ガイウス・ユリウス・カエサル（英語読みでシーザー）が初めて使ったことから、名称がついたシーザー暗号が有名です。
これは3文字分アルファベットをずらすという単一換字式暗号の一つです。次の暗号文は、このシーザー暗号を用いて暗号化しました。暗号文を解読してフラグを手にいれましょう。

暗号文: fsdz{Fdhvdu_flskhu_lv_fodvvlfdo_flskhu}
```
シーザー暗号のオンライン変換ツールにコピペしてフラグゲット。
[Dencode](https://dencode.com/ja/cipher/caesar)

---------------------

## ジャンル：[Reversing]
### Can you execute?

```
拡張子がないファイルを貰ってこのファイルを実行しろと言われたが、どうしたら実行出来るのだろうか。
この場合、UnixやLinuxのとあるコマンドを使ってファイルの種類を調べて、適切なOSで実行するのが一般的らしいが…
問題ファイル： exec_me
```

問題のファイルをダウンロードし、Linuxのfileコマンドで見てみる。  
```
exec_me: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2,
for GNU/Linux 2.6.24, BuildID[sha1]=663a3e0e5a079fddd0de92474688cd6812d3b550, not stripped
```
ELFファイルはLinuxの実行ファイル(Windowsでいうexe)。  
実行のため、パーミッションを変更し、実行してフラグゲット。

-----------------------

## ジャンル：[Misc]
### Can you open this file?
```
このファイルを開きたいが拡張子がないので、どのような種類のファイルで、どのアプリケーションで開けば良いかわからない。
どうにかして、この拡張子がないこのファイルの種類を特定し、どのアプリケーションで開くか調べてくれ。
問題ファイル： open_me
```

fileコマンドで見てみる。  
```
└─$ file open_me                                                             
open_me: Composite Document File V2 Document, Little Endian, Os: Windows, Version 10.0,
Code page: 932, Author: v, Template: Normal.dotm, Last Saved By: v, Revision Number: 1,
Name of Creating Application: Microsoft Office Word, Total Editing Time: 28:00, Create Time/Date: Mon Oct 12 04:27:00 2015,
Last Saved Time/Date: Mon Oct 12 04:55:00 2015, Number of Pages: 1, Number of Words: 3, Number of Characters: 23, Security: 0
```

とあるので、たぶんWordだろう。  
拡張子を.docに変更し、Wordで開く。  
Wordは購入していないので、Microsoft 365 オンラインのWordで開くとフラグゲット。

-----------------------

## ジャンル：[Web]
### HTML Page
```
HTML(Hyper Text Markup Language)は、Webサイトを記述するための言語です。
ページに表示されている部分以外にも、ページをより良くみせるためのデータが含まれています。
次のWebサイトからフラグを探して下さい。
http://q9.ctf.cpaw.site
```

サイトにアクセスしソースを表示する。  
HTMLヘッダーにフラグが書いてある。  

---------------------
## ジャンル：[Forensics]
### River
```
JPEGという画像ファイルのフォーマットでは、撮影時の日時、使われたカメラ、位置情報など様々な情報(Exif情報)が付加されることがあるらしい。
この情報から、写真に写っている川の名前を特定して欲しい。
問題ファイル： river.jpg

FLAGの形式は、"cpaw{river_name}"
例：隅田川 → cpaw{sumidagawa}
```
Exif情報を見てみると、緯度経度がある。  
Googlemapに入力し河川名からフラグゲット。
