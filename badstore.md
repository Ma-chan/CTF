# BadStoreへの脆弱性診断の練習
- 大学院でburpsuiteを使っていたので、burpの拡張機能であるBadStoreの脆弱性診断をして、練習してみることにした。

### 環境
- Kali-Linux(Debian派生Linux)
Kaliの中で、burpsuiteを起動させて、ブラウザのbadstoreへ脆弱性診断を行うことにした。

### 発見した脆弱性

- 検索欄にSQLI
検索欄をクリックすると、NO items matchedと表示される。SQL文が表示され、いかにもSQLIが効きそうなので、試してみる。

<p><img src="スクリーンショット 2022-05-05 18.05.07.png" alt="BadStore" /></p> 

MySQLを使用していることが判明.SQL文を挿入すると、ログイン後に操作できる買い物ページに遷移した。
<p><img src="スクリーンショット 2022-05-05 18.05.07.png" alt="BadStore" /></p> 
