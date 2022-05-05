# BadStoreへの脆弱性診断
- 大学院でburpsuiteを使っていたので、burpの拡張機能であるBadStoreの脆弱性診断をして、練習してみることにした。

### 環境
- Kali-Linux(Debian派生Linux)
Kaliの中で、burpsuiteを起動させて、ブラウザのbadstoreへ脆弱性診断を行うことにした。

### 発見した脆弱性

#### SQLI
検索欄をクリックすると、NO items matchedと表示される。SQL文が表示され、いかにもSQLIが効きそうなので、試してみる。

