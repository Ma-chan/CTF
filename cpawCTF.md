# CpawCTFのwriteup
前々からcpawCTFを解いていたが、文字に起こすことがなかったので、writeupを書いてみた。

### Test Problem
- 問題
この問題の答え（FLAG）は、cpaw{this_is_Cpaw_CTF} です。
下の入力欄にFLAGを入力してSubmitボタンを押して、答えを送信しましょう！

Enjoy CpawCTF!!!!
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
Ubuntuとかでflag出てくる。
### Can you open this file?
fileコマンドで拡張子を調べると、open_me: Composite Document File V2 Document, Little Endian, Os: Windowsとか出てくるので、Windowsを起動してみる。

### River 
答え


