# BadStoreへの脆弱性診断の練習
- 大学院でburpsuiteを使っていたので、burpの拡張機能であるBadStoreの脆弱性診断をして、練習してみることにした。

## ネタバレ
以下の脆弱性を試すことができるらしい

- クロスサイトスクリプティング(XSS)
- SQLI
- コマンドインジェクション
- クッキー/セクションポイズニング
- パラメータ/フォームの改ざん
- バッファオーバーフロー
- ディレクトリトラバーサル、強制ブラウジング
- Cookieスヌーピング
- ログ改ざん
- エラーメッセージの傍受
- サービス停止
- HTTP通信の書き換え
- などなど 

## 環境
- Kali-Linux(Debian派生Linux)
- OS macOS Mojave
Kaliの中で、脆弱性診断ツールの拡張機能であるbadstoreを起動させて、脆弱性診断を行うことにした。
<p><img src="スクリーンショット 2022-07-09 13.38.43.png" alt="BadStore_In_Kali" /></p> 

## 事前調査

- 攻撃を仕掛けていく前に、事前に狙えそうな脆弱性やアプリケーションの構成がどのようになっているのか調査する。

#### Webの隠しコンテンツの調査

- dirbコマンドで、webコンテンツをスキャンした。隠されたファイル名を取得することができた。
- favicon.ico <br>
imagesディレクトり <br>
index.html <br>
robots.txt <br>
supplierディレクトリ<br>
wsディレクトリ <br>
が存在することが判明した。

<p><img src="スクリーンショット 2022-05-05 19.05.55.png" alt="SQLI_vulnueablity" /></p> 

#### niktoの実行結果
- anti-clickjacking X-Frame-Options header is not present <br>

クリックジャッキングの脆弱性が判明した。表示されたWebサイトとは別のWebサイトを透過させ、表面に見えているサイトのボタンをクリックさせ、別のサイトのボタンを押させる攻撃への脆弱性が存在することがわかった

- The X-XSS-Protection header is not defined. <br>

X-XSS-ProtectionはXSSフィルタを設定するために使用されるもの。X-XSS-Protectionが設定されていない。XSSに対する脆弱性が存在しそう。

<p><img src="スクリーンショット 2022-05-05 19.06.43.png" alt="SQLI_vulnueablity" /></p> 

#### dirbの実行結果　

- 隠しコンテンツの列挙

<p><img src="スクリーンショット 2022-07-09 13.49.08.png" alt="SQLI_vulnueablity" /></p> 

## 攻撃

#### SQLI
- 検索欄をクリックすると、NO items matchedと表示される。SQL文が表示され、いかにもSQLIが効きそうなので、試してみる。
<p><img src="スクリーンショット 2022-05-05 18.05.07.png" alt="SQLI_vulnueablity" /></p> 
おきまりの'OR 1=1 --を入力すると、ログインしてからのみ閲覧可能なページに遷移する。
<p><img src="スクリーンショット 2022-05-05 18.23.45.png" alt="SQLI" /></p> 

メールアドレス登録の画面では、ユーザのメールアドレス、名前を見ることができた。
<p><img src="スクリーンショット 2022-05-05 18.31.57.png" alt="SQLI" /></p> 

#### XSS

- 検索窓にJSのスクリプトを埋め込むと、XSSが有効であることがわかる
<p><img src="スクリーンショット 2022-05-07 16.34.01.png" alt="XSS" /></p> 

- Guestbookも同様にXSSが有効であり、Add Entryボタンを押すと、過去のコメントが読める。
<p><img src="スクリーンショット 2022-07-02 9.45.01.png" alt="XSS" /></p>
<p><img src="スクリーンショット 2022-07-02 9.46.08.png" alt="XSS" /></p>

## Javaベースの対処法

- Javaを勉強したので、Javaだったらの対処法を書いた。

### SQLの静的プレースホルダ利用

Prepared Statementを使う。バインド機構使う。prepared statementを用いることで、静的なプレースホルダを使用することができ、あらかじめ">","<","/"などのスクリプトに関連する文字を避ける安全なsql文を使うことができる。

```java

String sql = “UPDATE bookinfo SET price= ? WHERE isbn= ?";

PreparedStatement ps = con.preparedStatement(sql);

```

### XSSのエスケープ処理・サニタイジング

スクリプト文で使用される">","<","&"なんかを退けるためのメソッドを追加してやると、入力フォームでスクリプトがエスケープされ、文字列として解釈され無効になる。

```java

 private static String escape(String val) {
       if (val == null) return "";
       val = val.replaceAll("&", "& amp;");
       val = val.replaceAll("<", "& lt;");
       val = val.replaceAll(">", "& gt;");
       val = val.replaceAll("\"", "&quot;");
       val = val.replaceAll("'", "&apos;");
       return val;
     }

```

### 今後やりたいこと

- ペンテスター向けの技術書を細々読んでいこうと思う。
