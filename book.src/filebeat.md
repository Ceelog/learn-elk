# Filebeat 日志收割

## Filebeat 服务配置

在 docker-compose.yaml 对于 Filebeat 服务的配置中：

```yaml
filebeat:
    build:
      context: filebeat/
      args:
        ELK_VERSION: $ELK_VERSION
    volumes:
      - "./filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro"
      - type: volume
        source: nginx-log
        target: /var/log
    depends_on:
      - nginx
    networks:
      - elk
```

- 使用 filebeat/ 目录下的 Dockerfile 构建镜像
- 数据卷映射：
    - 将宿主机目录下的配置文件映射到容器内
    - 使用宿主机上名为 nginx-log 的数据卷，并映射到容器内的 `/var/log` 目录，这和 Nginx 容器使用的是同一个目录
- Filebeat 服务依赖于 Nginx 服务，所以 Docker Compose 会先启动 Nginx ，然后再启动 Filebeat
- 使用名为 elk 的子网络，这样便可以访问同样使用这个子网络的其他容器

## Filebeat 日志收割配置
    
对于 `filebeat/filebeat.yml` 配置：

```yaml
{{#include ../docker-compose/filebeat/filebeat.yml}}
```

Filebeat 通过配置 input / processors / output 模块，实现日志收集、预处理、投递等工作

对于 input 模块：

- `type` 配置数据来源的类型，例如 文本日志 log 或者 控制台输出 stdout
- `paths` 指定日志的路径，可以使用模糊匹配，例如 `/var/log/*access.log`
- `encoding` 日志的编码，例如 utf-8
- `fields` 自定义添加的字段，例如 增加一个主题字段 `topic: nginx-log`
- `scan_frequency: 1s` 扫描日志文件的间隔
- `harvester_buffer_size: 16384` 日志收割缓存大小
- `tail_files: false` 启动 filebeat 后，是否从目标日志文件的结尾开始收集
- `close_eof: true` 采集日志到文件结尾的时候，是否关闭采集进程
- `clean_removed: true` 采集目标日志文件被移除后，是否从 filebeat registry 清楚

更全面的配置信息，请参考：
  - [https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-log.html](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-log.html)

对于 processors 模块：

- `drop_fields` 可以舍弃部分多余字段

对于 output 模块：

- `output.kafka` 将收集的日志投递到 Kafka 消息队列
- `hosts: ["kafka:9092"]` 指定 Kafka 服务的地址
- `topic: elk-%{[fields.topic]}` 指定投递到 Kafka 消息队列的频道
- `keep_alive: 60` Filebeat 和 Kafka 之间保持连接的时间
- `required_acks: 1` 日志消息投递后，是否需要等待 Kafka 的确认反馈

更全面的配置信息，请参考：
  - [https://www.elastic.co/guide/en/beats/filebeat/current/kafka-output.html](https://www.elastic.co/guide/en/beats/filebeat/current/kafka-output.html)

## 小结

Filebeat 启动后，开始监听目标文件，当发现新日志后就收集转发出去。