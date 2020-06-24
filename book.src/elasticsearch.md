# Elasticsearch 索引日志

## Elasticsearch 服务配置

在 docker-compose.yaml 对于 Elasticsearch 服务的配置中：

```yaml
  elasticsearch:
    build:
      context: elasticsearch/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - type: bind
        source: ./elasticsearch/config/elasticsearch.yml
        target: /usr/share/elasticsearch/config/elasticsearch.yml
        read_only: true
      - type: volume
        source: elasticsearch
        target: /usr/share/elasticsearch/data
    ports:
      - "9200:9200"
      - "9300:9300"
    environment:
      ES_JAVA_OPTS: "-Xmx512m -Xms512m"
      ELASTIC_PASSWORD: changeme
      # Use single node discovery in order to disable production mode and avoid bootstrap checks
      # see https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html
      discovery.type: single-node
    networks:
      - elk
```


- 使用 elasticsearch/ 目录下的 Dockerfile 构建镜像
- 数据卷映射：
    - 将宿主机目录下的配置文件映射到容器内
    - 使用宿主机上名为elasticsearch 的数据卷，并映射到容器内的 `/usr/share/elasticsearch/data` 目录，这样可以长久保存日志数据
- 使用名为 elk 的子网络，这样便可以访问同样使用这个子网络的其他容器
