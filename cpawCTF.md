# CpawCTFのwriteup
前々からcpawCTFを解いていたが、文字に起こすことがなかったので、writeupを書いてみた。
自分の覚書用。

### Test Problem
- 問題
この問題の答え（FLAG）は、cpaw{this_is_Cpaw_CTF} です。
下の入力欄にFLAGを入力してSubmitボタンを押して、答えを送信しましょう！Enjoy CpawCTF!!!!

- 答え
そのままflagを入力する </br>
### Classical Cipher
- 問題

- 答え　
Ceaser暗号で3つ文字をシフトさせてflag </br>

### Can you execute?
- 問題文 </br>
拡張子がないファイルを貰ってこのファイルを実行しろと言われたが、どうしたら実行出来るのだろうか。 </br>
この場合、UnixやLinuxのとあるコマンドを使ってファイルの種類を調べて、適切なOSで実行するのが一般的らしいが… </br>
問題ファイル： exec_me </br>
- 答え　</br>
fileコマンドで拡張子を確認する。ELFファイルなので、linux系のOSで実行してみる。Debianで初め実行してダメだった笑 </br>
chmodコマンドでファイル実行の許可を得てから、./exec_meで実行する。</br>
Ubuntuとかでflag出てくる。</br>

### Can you open this file?
- 答え　
fileコマンドで拡張子を調べると、open_me: Composite Document File V2 Document, Little Endian, Os: Windowsとか出てくるので、open_me.docとかにして、Microsoft Wordとかで開く。すると、flagがみれる。

### HTML Page
- 問題　
- 答え　
HTMLソース見ると、flagが見える。

### River 
- 答え
画像のGPS情報を確認する問題。Exif情報は画像に残っていることもあるので、Exif情報からGoogle Mapで場所を示してくれるサービスを適当に使って、場所特定。</br>
Exif-GPSとか使えば、Google Mapですぐ示してくれるので楽。Google Mapに経度と緯度を入力して特定してもok

### Q14.[PPC]並べ替えろ!
- 答え
pythonとか使って、バブルソート書いて降順に並び替えた。ただ、カンマとか空白とか色々めんどくさかった。

```python:bub_sort.py
def Bubble_Sort(arg):
    n=len(arg)
    for i in range(n):
        for j in range(n-1,i,-1):
          if arg[j] < arg[j-1]:
              tmp = int(arg[j])
              arg[j]=int(arg[j-1])
              arg[j-1]=tmp
    return arg

r=Bubble_Sort([15,1,93,52,66,31,87,0,42,77,46,24,99,10,19,36,27,4,58,76,2,81,50,102,33,94,20,14,80,82,49,41,12,143,121,7,111,100,60,55,108,34,150,103,109,130,25,54,57,159,136,110,3,167,119,72,18,151,105,171,160,144,85,201,193,188,190,146,210,211,63,207])
print(r)
```

これで実行すればいい。

### Hash Hash Hash
- 答え Googleで検索して、sha1 decryptとか調べればflag出てくる。

