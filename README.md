# ELK_Stack構築手順
1.docker-compose ELK+webサイト作成

2.資材を配置する

git cloneで資材をコピーしてくる

```
git clone https://github.com/nakaotaiki/ELK_Stack.git
```

3.ネットワーク作成

elasticstackを通信するためのネットワークを作成する。

```
docker network create elasticstack
```
```
docker network ls
```


4.ボリューム作成

elasticstackのデータを保存するためのボリュームを作成する。

```
docker volume create elasticstack
```

```
docker volume ls
```


5.docker-composeでコンテナを起動

docker-compose.ymlが存在するディレクトリで以下のコマンドを実行する。

```
docker-compose up -d
```

以下のように表示されればOK

```
Creating kibana        ... done

Creating elasticsearch ... done

Creating logstash      ... done

Creating mysql         ... done

Creating wordpress     ... done
```

6.コンテナ起動確認

以下のコマンドでコンテナが起動していることを確認する。

```
docker ps
```

7.Wordpressインストール

以下のURLからwordpressにログインし、初期設定する

<http://localhost:8000/>

言語設定画面から「日本語」を選択

「サイトのタイトル」>「ユーザ名」>「パスワード」>「メールアドレス」の順に必要な情報を入力する

「検索エンジンでの表示」にチェックをいれない

「Wordpressのインストール」を選択する

「成功しました！」と表示されるので、「ログイン」を選択

画面下の「サイト名へ移動」を選択し、作成したサイトにログインする

8.Apacheのアクセスログの更新確認

エクスプローラーを開き、docker-composeを実行したディレクトリ配下にあるApacheのアクセスログが更新されていることを確認する。

「\ELK2\data\apache2\access.log」

9.kibanaでデータ連携確認

以下のURLからkibanaにログインする

<http://localhost:5601/>

ApacheのアクセスログがElasticsearchに連携されていることを確認する

kibana画面から「Management」>「Index Management」を選択し、「access\_log」というインデックスが存在することを確認する。

10.インデックスパターン作成

kibana画面から「Management」>「Index Patterns」> 「Create Index Pattern」を選択する

「Index Patterns」欄に「access\_log」と入力し、「Next Step」を選択する。

「Time Filter field nam」欄に「@timestamp」を選択し、「Create Index Pattern」を選択する。

11.連携データ確認

kibana画面から「Discover」を選択し、連携されたアクセスログの内容を確認する。

12.グラフ作成

kibana画面から「Visualize」>「Create New Visualization」>「Horizonal Bar」>「access.log」を選択

「Bucket」項目の「add」>「Split Chart」を選択する。

「Agrregation」：「Terms」

「Field」:「apache.access.remote\_ip.keyword」を選択し、「▷」をクリックする。

グラフが表示されることを確認する。


参考サイト

https://qiita.com/kenchan1193/items/9320390d48f3d2ae883c

https://noumenon-th.net/programming/2019/04/05/docker-wordpress/

