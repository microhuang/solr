## search模块preference分析（ES6.2+）

> 官方文档：https://www.elastic.co/guide/en/elasticsearch/reference/5.4/search-request-preference.html

> 实现：https://github.com/elastic/elasticsearch/blob/6.2/server/src/main/java/org/elasticsearch/cluster/routing/OperationRouting.java

> 实现：https://github.com/elastic/elasticsearch/blob/6.2/server/src/main/java/org/elasticsearch/cluster/routing/IndexShardRoutingTable.java

其中：
```
#......非常高效的Murmur实现
int routingHash = Murmur3HashFunction.hash(preference);
#......
return indexShard.activeInitializingShardsIt(routingHash);
#......
return new PlainShardIterator(shardId, shuffler.shuffle(activeShards, seed));
#......轮询洗牌（分片）
this.shuffler = new RotationShardShuffler(Randomness.get().nextInt());
#......
activeShards.add(shard);
```

* 为了更稳定精准的性能控制，需要改轮询洗牌为一致性HASH实现
* 非常遗憾，ES的定制开发支持比Solr差了太远！

