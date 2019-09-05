# android向けfirefoxの拡張機能開発方法の簡単まとめ
android向けfirefoxの拡張機能を開発する環境を構築したときのメモ  
webextensionの書き方とかは別の記事で、今回は拡張機能をパッケージして、実際にアンドロイド端末でテストできるようにする土台を構築します。


## 環境構築
### 1.node.jsをインストールする(win版)<br>
インストールされてるか、できたかの確認コマンド

```
node --version
```

https://nodejs.org/ja/
からダウンロード、インストール

ファイル名検索から

```
node
```

で検索して
Node.js command prompt
を選択。

### 1.5.ショートカットで楽に起動できるようにする
タスクバーのnode.js command promptを右クリック  
一番上のnode.js command promptを右クリック  
プロパティを選択  
リンク先　を全部コピー  
ショートカットを作りたいディレクトリで右クリック→新規作成→ショートカット
リンク先にさっきコピーした文をペーストして完成

### 2.web-extをインストールする<br>

```
npm install --global web-ext
```

確認（バージョンが表示されたらOK）

```
web-ext --version
```

## アドオンのパッケージ化～実行
### パッケージ化
manifest.jsonがあるディレクトリで
```
web-ext build
```
で終わり。  
web-ext-artifacts内に作成されたパッケージ(zipファイル)が保存される。  
