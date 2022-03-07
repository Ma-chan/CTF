# CpawCTFのwriteup
### レベル1
そのままflagを入力する </br>
### レベル2
Ceaser暗号で3つ文字をシフトさせてflag </br>
### Can you execute?
fileコマンドで拡張子を確認する。ELFファイルなので、linux系のOSで実行してみる。 </br>
chmodコマンドでファイル実行の許可を得てから、./exec_meで実行する。</br>
