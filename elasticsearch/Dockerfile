#FROM elasticsearch:7.6.2
FROM docker.elastic.co/elasticsearch/elasticsearch:7.6.2

COPY ./config/elasticsearch.yml /usr/share/elasticsearch/config/elasticsearch.yml

# kuromojiをインストール
RUN elasticsearch-plugin install analysis-kuromoji
