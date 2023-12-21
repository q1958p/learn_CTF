# Writeup

## ジャンル：General Skills
### Obedient Cat
```
This file has a flag in plain sight (aka "in-the-clear"). Download flag.
```

Download flagにファイルへのリンクが設定されている。  
ダウンロードしファイルを開くだけ。

### Python Wrangling
```
Python scripts are invoked kind of like programs in the Terminal...
Can you run this Python script using this password to get the flag?
```

Pythonスクリプト、パスワードファイル、暗号化されたフラグファイルの3つがリンクされている。  
PythonスクリプトのUsageや-hで実行方法を見て、フラグファイルを復号する。  

### Wave a flag
```
Can you invoke help flags for a tool or binary? This program has extraordinarily helpful information...
```

Linux実行ファイルへのリンクがある。  
-hでヘルプを表示するだけ。  

### Nice netcat...
```
There is a nice program that you can talk to by using this command in a shell: $ nc mercury.picoctf.net 49039, but it doesn't speak English...
```
指示通りアクセスする。  
数字が表示されるので、ASCIIコード変換しFlagゲット。

-----

## ジャンル：Cryptography
### Mod 26
```
Cryptography can be easy, do you know what ROT13 is?
cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_uJdSftmh}
```

ROT13による暗号化。  
RTO13で検索すればオンラインデコーダがある。


------

## ジャンル：Forensics
### Information
```
Files can always be changed in a secret way. Can you find the flag? cat.jpg
```

ぬこの画像。
背景のディスプレイ、Exif情報、バイナリから"FLAG","flag","pico","ctf"等検索、stringsコマンドで文字列を列挙、binwalkで内部にファイルが無いか確認。
が、めぼしい情報なし。  

exiftoolで詳細を確認。  
Licenseにランダム文字列はおかしくない？  
Licenseにある文字列が不自然なことに気付けるかどうか。  
base64デコードしてFlagゲット。

画像右上の"MDY02 EPI 6037"はLINEcameraのフレームらしい。


-------
## ジャンル：Reverse Engineering
### Transformation
**! 要再挑戦**

encの中身を文字列にして、問題文のpythonコードを実行したがエラーとなった。
1文字目の"灩"をバイナリで見ると、"0b0b111000001101001"。
上位8bitと下位8bitで分割すると
上位:"0b01110000"->p
下位:"0b01101001"->i
となりフラグが1文字に内包されていることがわかる。

上位と下位をmaskしてprintしflagゲット。
