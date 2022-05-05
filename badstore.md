# BadStoreへの脆弱性診断の練習
- 大学院でburpsuiteを使っていたので、burpの拡張機能であるBadStoreの脆弱性診断をして、練習してみることにした。

### 環境
- Kali-Linux(Debian派生Linux)
Kaliの中で、脆弱性診断ツールの拡張機能であるbadstoreを起動させて、脆弱性診断を行うことにした。

### 発見した脆弱性

#### Webの隠しコンテンツの調査
- dirbコマンドで、webコンテンツをスキャンした。隠されたファイル名を取得することができた。
- favicon.ico,imagesディレクトリ、index.html,robots.txt,supplierディレクトリ,wsディレクトリが存在することが判明した。
<p><img src="スクリーンショット 2022-05-05 19.05.55.png" alt="SQLI_vulnueablity" /></p> 

#### Niktoの実行結果
- anti-clickjacking X-Frame-Options header is not present <br>

クリックジャッキングの脆弱性が判明した。表示されたWebサイトとは別のWebサイトを透過させ、表面に見えているサイトのボタンをクリックさせ、別のサイトのボタンを押させる攻撃への脆弱性が存在することがわかった

- The X-XSS-Protection header is not defined. <br>

X-XSS-ProtectionはXSSフィルタを設定するために使用されるもの。X-XSS-Protectionが設定されていない。XSSに対する脆弱性が存在しそう。

<p><img src="スクリーンショット 2022-05-05 19.06.43.png" alt="SQLI_vulnueablity" /></p> 

#### SQLI
- 検索欄をクリックすると、NO items matchedと表示される。SQL文が表示され、いかにもSQLIが効きそうなので、試してみる。
<p><img src="スクリーンショット 2022-05-05 18.05.07.png" alt="SQLI_vulnueablity" /></p> 
おきまりの'OR 1=1 --を入力すると、ログインしてからのみ閲覧可能なページに遷移する。
<p><img src="スクリーンショット 2022-05-05 18.23.45.png" alt="SQLI" /></p> 

メールアドレス登録の画面では、ユーザのメールアドレス、名前を見ることができた。
<p><img src="スクリーンショット 2022-05-05 18.31.57.png" alt="SQLI" /></p> 
