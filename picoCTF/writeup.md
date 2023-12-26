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

### Static ain't always noise  

バイナリーファイルとbashスクリプトが与えられる。  
bash実行環境でスクリプトを実行すると、引数にファイルが必要なエラーが表示される。  
バイナリーファイルを引数で与えるとテキストファイルが作成され、中にフラグが書いてある。  

### Tab, Tab, Attack  

zipが与えられる。  
zipには長い名前のディレクトリ階層構造になっている。
TAB補完でディレクトリに潜って行き、最後の実行ファイルに実行権限を与えて実行するとフラグゲット。  

------------------

## ジャンル：Cryptography
### Mod 26
```
Cryptography can be easy, do you know what ROT13 is?
cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_uJdSftmh}
```

ROT13による暗号化。  
RTO13で検索すればオンラインデコーダがある。

### Mind you Ps and Qs  

RSA暗号を解く問題。  
暗号文c, 公開鍵Nとeが与えられる。

[RSA Decoder](https://www.dcode.fr/rsa-cipher)
を利用してフラグゲット。  

RSAの仕組みについて復習が必要。  

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

### Matryoshka doll
マトリョーシカのpng画像が与えられる。  
タイトルや説明文の通り、png画像に別のファイルが埋め込まれているのだろう。  

Stirlingでバイナリを見てみる。  
PNGのファイルフォーマット先頭は、  
```
89 50 4E 47
.  P  N  G
```
末尾はIENDである。  

IENDを検索するとまだデータが続いている。
PKほにゃららとある。  
PKほにゃららはZIPのファイルフォーマット。
```
[先頭]
0x  50 4B 03 04
    P  K  03 04

[末尾]
0x  50 4B 05 06
    P  K  05 06
まだ数バイト続くが、一応終端。
```

PNG~IENDを削除し、適当な名前.zipで保存する。　

ZIPを開くとさらに画像があり、同じことを繰り返していくと、最終的にテキストが入ったZIPとなり、フラグゲット。  


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

### keygenme-py

Pythonスクリプトが与えられる。  
実行すると、入力値によって動作が変化するようだ。  
その中にLicense認証のような箇所がある。  

```
username_trial = "ANDERSON"
bUsername_trial = b"ANDERSON"

key_part_static1_trial = "picoCTF{1n_7h3_|<3y_of_"
key_part_dynamic1_trial = "xxxxxxxx"
key_part_static2_trial = "}"
key_full_template_trial = key_part_static1_trial + key_part_dynamic1_trial + key_part_static2_trial
```

```
def check_key(key, username_trial):

    global key_full_template_trial

    if len(key) != len(key_full_template_trial):
        return False
    else:
        # Check static base key part --v
        i = 0
        for c in key_part_static1_trial:
            if key[i] != c:
                return False

            i += 1

        # TODO : test performance on toolbox container
        # Check dynamic part --v
        if key[i] != hashlib.sha256(username_trial).hexdigest()[4]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[5]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[3]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[6]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[2]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[7]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[1]:
            return False
        else:
            i += 1

        if key[i] != hashlib.sha256(username_trial).hexdigest()[8]:
            return False



        return True
```

License認証処理は、前半の
```
key_part_static1_trial = "picoCTF{1n_7h3_|<3y_of_"
```
の一致確認と、後半の8バイトをsha256で変換した16進数を1バイトずつ4->5->3->6->2->7->1->8バイト目が一致しているか確認している。  
sha256でハッシュかするのはユーザー名の"ANDERSON"なので、同じように1バイトずつ表示し、前半と結合してフラグゲット。  

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
