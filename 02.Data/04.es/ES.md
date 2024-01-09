<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [关于](#关于)
- [QA](#qa)

<!-- /TOC -->
</details>

## 关于


## QA

max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

```bash
echo 'vm.max_map_count=655360' >> /etc/sysctl.conf
sudo sysctl -p
```

max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]

```bash
echo 'es hard nofile 65536\nes soft nofile 65536' >> /etc/security/limits.conf
```

the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured

```yml
node.name: node-1
cluster.initial_master_nodes: ["node-1"]
```

can not run elasticsearch as root

```bash
adduser elasticsearch
passwd elasticsearch
chown -R elasticsearch /ope/install/es
```



```yml
version: '3'
services:
    elasticsearch:
    image: elasticsearch:6.4.0
    container_name: elasticsearch
    environment:
      - "cluster.name=elasticsearch"
      - "discovery.type=single-node"
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - TZ=Asia/Shanghai
    volumes:
        - /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins
        - /mydata/elasticsearch/data:/usr/share/elasticsearch/data
    ports:
        - 9200:9200
        - 9300:9300
    kibana:
        image: kibana:6.4.0
        container_name: kibana
        links:
            - elasticsearch:es
        depends_on:
            - elasticsearch
        environment:
            - "elasticsearch.hosts=http://es:9200"
            - TZ=Asia/Shanghai
        ports:
            - 5601:5601
    logstash:
        image: logstash:6.4.0
        container_name: logstash
        environment:
            - TZ=Asia/Shanghai
        volumes:
            - /mydata/logstash/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
        depends_on:
            - elasticsearch
        links:
            - elasticsearch:es
        ports:
            - 4560:4560
            - 4561:4561
            - 4562:4562
            - 4563:4563
```