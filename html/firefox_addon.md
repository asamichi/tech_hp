# android向けfirefoxの拡張機能開発方法の簡単まとめ
android向けfirefoxの拡張機能を開発する環境を構築したときのメモ  
webextensionの書き方とかは別の記事で、今回は拡張機能をパッケージして、実際にアンドロイド端末でテストできるようにする土台を構築します。


## 環境構築
### 0.androidに開発テスト用のfirefoxをインストールする
firefox Nightlyをアンドロイド端末にインストールしてください。  
このファイアフォックスでテスト等を行います。  
普段firefoxを使っている人もいない人も、普段使いのぐちゃぐちゃした環境と切り離すため、テスト専用としてこちらを利用することをおすすめします。

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
### パッケージ化(非署名)
署名無しならmanifest.jsonがあるディレクトリで
```
web-ext build
```
で終わり。  
ただし、色々制限があってやりにくいので署名推奨。  
下記のようにxpiを直接投げてテストする場合は署名必須なので注意  
web-ext-artifacts内に作成されたパッケージ(zipファイル)が保存される。  

## パッケージ化（署名有り）
署名のために開発者登録をする
https://addons.mozilla.org/en-US/developers/addon/api/key/  
登録してapi_keyを取得しておく  
jwt issuer　とjwt secretが発行される

以下のコマンドを、node.js command promptで実行する。

```
web-ext sign --api-key=$AMO_JWT_ISSUER --api-secret=$AMO_JWT_SECRET 
```

web-ext-artifactsというディレクトリが作成され、その下にxpiファイルが作成されます。

生成されたxpiをドロップボックスやHPにアップロードします。  
アンドロイド端末でアクセスできる場所なら何でもいいです。  
web-ext-artifacts内に生成されたxpiファイルをアップロードしてください。  
アップロードしたxpiファイルを、android端末からドロップボックスアプリ（開くアプリ選択でfirefoxを選択）なりファイアフォックスで直接アクセスすれば無事、アドオンを導入できます。  

web-ext signを実行するたびに、manifest.jsonのversionを変更する必要があります。  
versionを変更しないと、version exitstエラーが出て、xpiを生成できません。


## エラーとかコンソール見ながらデバッグしよう
firefox(36以降)の開発ツールをAndroid版firefoxに接続する方法。古いFirefoxのバージョンだとやり方が違うようなので注意。２０１９年１０月時点で最新のやり方がこれ。一番ラクなのもこれ（adbとか使わなくて済む）

PC側のfirefoxを開いて

about:debugging

を開く。セットアップ→リモートデバイスへの接続　を行う。
Android側でもファイアフォックス起動、設定からリモートデバッグを許可する。

接続後、左の方にアンドロイド端末が表示されるので、接続を選択。端末名をクリックでAndroid側の情報が表示されるようになる。

PC側のFirefoxに、Android側のFirefoxの情報（開いているタブ、導入されているアドオン等）が表示されるため、デバッグしたい項目を選択する。

デバッグしたいアドオン名等の欄の調査を選択するとコンソールが出る。ここに console.log()を使って変数やら文字列を表示するといわゆるprintfデバッグができる。