# Kafka

## Consumer グループ / パーティション

### 基本
- トピックを分担して処理する単位が Consumer グループ
  - あるメッセージは同じ Consumer グループ内では1つの Consumer だけが購読できる（1つの Consumer だけに配信される）O'Reilly Kafka では「サブセット」という表現をしている
  - 別の Consumer グループを設けるとそのグループは他のグループとは独立してトピックを購読できる。aws SNS/SQS の組み合わせで fan-out したような挙動をする
- 同一 Consumer グループの Consumer はパーティション数まで増やせる
  - パーティション数以上の Consumer を起動してもアイドルになりメッセージを購読しない
  - Consumer の追加・減少が起きると担当パーティションの再編が行われる。リバランスと言う
  - リバランスが起きると短時間だが Consumer グループ全体の処理が停止する。途中で追加するのはできれば避けた方がよい。1プロセスでも障害が起きると短時間完全に処理が停止する
- 逆に Consumer にどのパーティションを処理するか指定する運用方法もある
  - スタンドアロン Consumer と言う
  - Consumer グループと自動割当を使わず1つ1つの Consumer にどのパーティションを処理するか指示する

### オフセット
- Consumer は Consumer グループがメッセージを読んだ位置を `__consumer_offsets` トピックにコミットする
  - MySQL みたいに自動コミットもあるが普通はきちんと処理できたときにコミットする
  - コミットされてる値と実際に処理した位置がずれると、同じメッセージが2回処理されたり、メッセージが処理されなかったりする

### どのような設定にすべきか
- 不要なリバランスを避ける方法・安全にリバランスを行う方法は O'Reilly Kafka の第4章
- パーティション数の設定方法は O'Reilly Kafka の第2章



## 運用プラクティス

- [Kafkaを使った マイクロサービス基盤 part2 ＋運用して起きたトラブル集](https://www.slideshare.net/matsu_chara/kafka-part2)
- [Apache Kafkaを使ったアプリ設計で反省している件を正直ベースで話す](https://medium.com/@laqiiz/apache-kafka%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%9F%E3%82%A2%E3%83%97%E3%83%AA%E8%A8%AD%E8%A8%88%E3%81%A7%E5%8F%8D%E7%9C%81%E3%81%97%E3%81%A6%E3%81%84%E3%82%8B%E4%BB%B6%E3%82%92%E6%AD%A3%E7%9B%B4%E3%83%99%E3%83%BC%E3%82%B9%E3%81%A7%E8%A9%B1%E3%81%99-6116544cc6fd) - 運用ではなくアプリ設計の話でなかなかおもしろい
- [Apache Kafkaを使ったマイクロサービス基盤](https://xuwei-k.github.io/slides/kafka-matsuri/#1)
- [サイボウズのログ基盤 2018年版](https://blog.cybozu.io/entry/2018/03/19/080000)

## [Apache Kafka: デプロイメントを最適化するための10のベストプラクティス](https://www.infoq.com/jp/articles/apache-kafka-best-practices-to-optimize-your-deployment/)
- NAS はだめらしい。プラット Kafka が k8s で local disk を使っているのは理にかなっている
