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

もしESが再起動を繰り返して以下のようなログが出ている場合、docker-cmposeを起動したディレクトリで以下コマンドを実行する

エラー

```
elasticsearch1    | "stacktrace": ["org.elasticsearch.bootstrap.StartupException: ElasticsearchException[failed to bind service]; nested: AccessDeniedException[/usr/share/elasticsearch/data/nodes];",
```

実行コマンド
```
sudo chown -R 1000:1000 data/
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

![image](https://user-images.githubusercontent.com/73123382/205108297-3316c8da-515e-4cb8-9c37-8359a18e8c01.png)

8.Apacheのアクセスログの更新確認

エクスプローラーを開き、docker-composeを実行したディレクトリ配下にあるApacheのアクセスログが更新されていることを確認する。

・data\apache2\access.log

9.kibanaでデータ連携確認

以下のURLからkibanaにログインする

<http://localhost:5601/>

ApacheのアクセスログがElasticsearchに連携されていることを確認する

kibana画面から「Management」>「Index Management」を選択し、「access\_log」というインデックスが存在することを確認する。
![image](https://user-images.githubusercontent.com/73123382/205113717-8fe77e51-7a14-4c2d-9ae2-4fc774c662cb.png)

10.インデックスパターン作成

kibana画面から「Management」>「Index Patterns」> 「Create Index Pattern」を選択する
![image](https://user-images.githubusercontent.com/73123382/205113873-8aa56217-3f9d-4ef8-8ffe-bf11f51d3955.png)


「Index Patterns」欄に「access_log」と入力し、「Next Step」を選択する。
![image](https://user-images.githubusercontent.com/73123382/205114060-8efd2a3d-3395-401a-9486-565bca9dd57e.png)


「Time Filter field nam」欄に「@timestamp」を選択し、「Create Index Pattern」を選択する。
![image](https://user-images.githubusercontent.com/73123382/205114140-928528b2-e1b6-4a39-8d21-783c22ab6fa0.png)


11.連携データ確認

kibana画面から「Discover」を選択し、連携されたアクセスログの内容を確認する。
![image](https://user-images.githubusercontent.com/73123382/205114376-bfb7c41c-3cdf-4e38-bc12-57df2a4ce901.png)


12.グラフ作成

kibana画面から「Visualize」>「Create New Visualization」>「Horizonal Bar」>「access.log」を選択
![image](https://user-images.githubusercontent.com/73123382/205114565-d756a465-7712-4b73-82bd-402b436f6134.png)


「Bucket」項目の「add」>「Split Chart」を選択する。

「Agrregation」：「Terms」

「Field」:「apache.access.remote_ip.keyword」を選択し、「▷」をクリックする。

グラフが表示されることを確認する。
![image](https://user-images.githubusercontent.com/73123382/205114979-de7aff85-69ea-4d85-ba3b-3784869d5318.png)


参考サイト

https://qiita.com/kenchan1193/items/9320390d48f3d2ae883c

https://noumenon-th.net/programming/2019/04/05/docker-wordpress/

