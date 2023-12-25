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

-------
## ジャンル：Binary Exploitation
### Stonks
```
I decided to try something noone else has before. I made a bot to automatically trade stonks for me using AI and machine learning.
I wouldn't believe you if you told me it's unsecure! vuln.c nc mercury.picoctf.net 20195
```

**! 要再挑戦**  

Format String Attackとはprintf(), syslog(), err()など、書式を用いた関数に関する脆弱性。  
printf(str)のように、書式を使用しないと、printf()の出力内容をコントロールされる。  
%xや%pによりメモリを表示できたりする。
  
vuln.cにはprintf(user_buf);のようにFormat String脆弱性がある。  
user_bufに%pを複数渡すことでスタックが見れる。  
```c:vuln.c
	printf("What is your API token?\n");
	scanf("%300s", user_buf);
	printf("Buying stonks with token:\n");
	printf(user_buf);
```

スタックの中に以下の部分がある。  
```
0x6f636970
0x7b465443
0x306c5f49
0x345f7435
0x6d5f6c6c
0x306d5f79
0x5f79336e
0x35343036
0x64303664
0xffa7007d
```
これを1バイトずつ文字に直すと、
```
ocip{FTC0l_I4_t5m_ll0m_y_y3n5406d06d}
```

[CyberChef](https://gchq.github.io/CyberChef/ "CyberChef")  
で"To hex"->"Swap endianness"->"From Hex"で変換し、  
```
picoCTF{I_l05t_4ll_my_m0n3y_6045d60d}
```

----------
## ジャンル：Web Exploitation
### GET aHEAD  
```
Find the flag being held on this server to get ahead of the competition http://mercury.picoctf.net:28916/
```

Hinsを見ると"リクエストを変更しレスポンスを見たら？"とある。  
タイトルからHEADリクエストっぽいので、curlでHEADリクエストを送信し、フラグゲット。  
```
curl -I http://...
```
