## ES安装ik分词插件

### 集群
```
docker-compose exec es01 elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.5.1/elasticsearch-analysis-ik-7.5.1.zip
```

```
docker-compose exec es02 elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.5.1/elasticsearch-analysis-ik-7.5.1.zip
```

### 然后要重启es容器
```
docker-compose restart es01
docker-compose restart es02
```

### 验证分词插件是否安装成功

增加一个叫test001的索引
```
curl -X PUT http://localhost:9200/test001
// 成功返回 {"acknowledged":true,"shards_acknowledged":true,"index":"test001"}
```

ik_smart分词
```
curl -X POST \
'http://127.0.0.1:9200/test001/_analyze?pretty=true' \
-H 'Content-Type: application/json' \
-d '{"text":"我们是软件工程师","tokenizer":"ik_smart"}'
```

ik_max_word分词
```
curl -X POST \
'http://127.0.0.1:9200/test001/_analyze?pretty=true' \
-H 'Content-Type: application/json' \
-d '{"text":"我们是软件工程师","tokenizer":"ik_max_word"}'
```


## ES安装HEAD插件

1. 下载elasticsearch-head
```
cd elasticsearch
git clone git://github.com/mobz/elasticsearch-head.git
mv elasticsearch-head head
```

2. elasticsearch\head\Gruntfile.js修改以下片段
```
connect: {
    server: {
        options: {
            /* 默认监控：127.0.0.1,修改为：0.0.0.0 */
            hostname: '0.0.0.0',
            port: 9100,
            base: '.',
            keepalive: true
        }
    }
}
```
3. elasticsearch\head\\_site\app.js修改以下代码片段
```
* 修改localhost为elasticsearch集群地址，Docker部署中，一般是elasticsearch宿主机地址 */
this.base_uri = this.config.base_uri || this.prefs.get("app-base_uri") || "http://localhost:9200";
```
