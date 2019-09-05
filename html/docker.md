度々docker使うたびに調べてる＋一回軌道に乗ったらしばらくコマンドコピペ→しばらくしてまたdocker使うときに調べる…<br>
というのが効率悪いので自分用に作ったdockerまとめ。<br>
<br>
# チートシート
<pre>
$ docker ps  #起動しているコンテナ一覧.　–aで止まってるコンテナも見える.－ｑでIDだけ表示
$ docker images #エンジンにあるイメージ一覧. –qでIdだけ表示
$ docker run -d -p 8800:80 --name コンテナ名 -v $(pwd)/html:/var/www/html --privileged イメージ名：タグ /sbin/init
#家のwinでは最後の/sbin/initはあるとダメだった。ルートは権限系で弾かれた
#マウント時パスに：が入るとダメ。//でパスを開始すると回避できる。以下例
$ docker run -d -p 8800:80 --name rabo -v //c/Users/Owner/docker/RTOS://home/sim --privileged centos //sbin/init

#マウントしてポート8800→80につないで実行
$ docker exec –it name bash     #コンテナ名nameのbashに入る winpty～で怒られるなら winpty docker execで
$ docker build –t name:tag .       #dockerfileからコンテナ作成
$ docker commit コンテナ名 イメージ名:タグ  #コンテナをイメージ化
$ docker  pull リポジトリ    #イメージをダウンロードする。pushでアップロード

$ docker start コンテナ名          #コンテナを起動
$ docker stop コンテナ名           #コンテナを止める
$ docker rm コンテナ名             #コンテナを消す(必ず止めてから消す)
$ docker stop $(docker ps –q) 　#起動しているコンテナを全部止める
$ docker rm $(dockere ps –q)   #起動しているコンテナを全部消す
$ docker rmi イメージ名　#イメージの削除.イメージが持ってるコンテナをすべて消す必要がある.

コンテナから抜けるときは Ctrl + P + Q

</pre>
# 最初に
<pre>
Dockerを起動
　C:\Program Files\Docker Toolbox\start.sh
</pre>
## Dockerコマンド一覧
<pre>
・イメージの確認
docker images

・コンテナの確認
docker ps -a

・コンテナの立ち上げ
docker run -p 8080:80 -d --name service1 --privileged centos 
docker run [op]  [image name:tag] [CM]  :
 コンテナをイメージから作成する
 [op]はコマンド群。[image name:tag]はcentosのところ。tagは省略すると自動でlatest。
 [CM]はコンテナ作成後に実行するコマンド。　
-p num1:num2 :
 ホストOSのポートnum1に来たリクエストをコンテナのポートnum2に転送する（ポートフォワーディング）
--name [name]:
コンテナに名前を付ける（自動で割り振られるIDではなく名前で操作できるようにする）
--privileged：
権限の付与。コンテナ内でsystemctlコマンドが使えるように。
-d：バックグラウンドで起動する。

・マウントのあれこれ
コンテナ作成後にマウントの色々は変えれない
例　./のhtmlを/var/www/htmlにマウント
$ mkdir html
$ docker run –d –p 8082:80 --name service3 –v $(pwd)/html:/var/www/html --privileged service:1.0 /sbin/init
-v [pass A]:[pass B]  :ホスト側のpass Aのディレクトリをコンテナ内部のpass B のディレクトリにマウントする。


・コンテナに接続
docker exec -it service1 bash
</pre>
## dockerfile関連
<pre>
簡単なdockerfile
FROM centos
MAINTAINER name　　　　　　　　　　#nameはあなたの名前を入れてください

RUN yum install httpd –y　　　　　　 #apacheのインストール　
#apacheがサーバ起動時に自動起動するように設定
RUN systemctl enable httpd.service   

Dockerfileの主なコマンド
FROM:ベースとするDockerイメージ
ローカルに無いイメージでも勝手に
ダウンロードしてくれる
RUN：セットアップ時のOSのコマンド
ADD：ADD　passA passBで passAのtarファイルを
コンテナ内のBにコピーして展開する
COPY:ADDと似た挙動でtarの展開はせずコピーのみ
CMD：コンテナ起動時に実行するコマンド

dockerfileからイメージ生成
$ docker build -t service:1.0 .
コマンドの最後にドットがあることに注意してください
docker build [op] [docker file directory]
-t:作成したイメージに名前を付ける
 -t name:tag
docker file directoryはdockerfileの場所を指定します。
相対パスも絶対パスも可能です。
</pre>
## 履歴確認
<pre>
$ docker history service:1.0
</pre>


