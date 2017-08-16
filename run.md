#### build
```bash
➜  docker-elk git:(master) ✗ docker-compose build
Building elasticsearch
Step 1/1 : FROM docker.elastic.co/elasticsearch/elasticsearch:5.5.1
 ---> 74ef44f69db6
Successfully built 74ef44f69db6
Successfully tagged dockerelk_elasticsearch:latest
Building kibana
Step 1/1 : FROM docker.elastic.co/kibana/kibana:5.5.1
 ---> 7bf53f710f44
Successfully built 7bf53f710f44
Successfully tagged dockerelk_kibana:latest
Building logstash
Step 1/1 : FROM docker.elastic.co/logstash/logstash:5.5.1
 ---> e8f5c2ec30f2
Successfully built e8f5c2ec30f2
Successfully tagged dockerelk_logstash:latest
➜  docker-elk git:(master) ✗ docker images | grep dockerelk
#目录
dockerelk_kibana                                latest              7bf53f710f44        3 weeks ago         629MB
dockerelk_logstash                              latest              e8f5c2ec30f2        3 weeks ago         577MB
dockerelk_elasticsearch                         latest              74ef44f69db6        3 weeks ago         544MB
➜  docker-elk git:(master) ✗
```

#### test-elasticsearch
```bash
elasticsearch:
docker run --rm -it -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xmx256m -Xms256m" \
	-v `pwd`/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
	dockerelk_elasticsearch

➜  docker-elk git:(master) ✗ docker run --rm -it -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xmx256m -Xms256m" \
        -v `pwd`/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
        dockerelk_elasticsearch
[2017-08-16T09:51:54,678][INFO ][o.e.n.Node               ] [] initializing ...
[2017-08-16T09:51:54,824][INFO ][o.e.e.NodeEnvironment    ] [nOyaNt9] using [1] data paths, mounts [[/ (none)]], net usable_space [37.3gb], net total_space [59gb], spins? [possibly], types [aufs]
[2017-08-16T09:51:54,825][INFO ][o.e.e.NodeEnvironment    ] [nOyaNt9] heap size [247.5mb], compressed ordinary object pointers [true]
[2017-08-16T09:51:54,828][INFO ][o.e.n.Node               ] node name [nOyaNt9] derived from node ID [nOyaNt9wSE2M0X3CqtNKzA]; set [node.name] to override
[2017-08-16T09:51:54,829][INFO ][o.e.n.Node               ] version[5.5.1], pid[1], build[19c13d0/2017-07-18T20:44:24.823Z], OS[Linux/4.9.36-moby/amd64], JVM[Oracle Corporation/OpenJDK 64-Bit Server VM/1.8.0_141/25.141-b16]
[2017-08-16T09:51:54,830][INFO ][o.e.n.Node               ] JVM arguments [-Xms2g, -Xmx2g, -XX:+UseConcMarkSweepGC, -XX:CMSInitiatingOccupancyFraction=75, -XX:+UseCMSInitiatingOccupancyOnly, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -Djdk.io.permissionsUseCanonicalPath=true, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Dlog4j.skipJansi=true, -XX:+HeapDumpOnOutOfMemoryError, -Des.cgroups.hierarchy.override=/, -Xmx256m, -Xms256m, -Des.path.home=/usr/share/elasticsearch]
....
[2017-08-16T09:51:57,156][INFO ][o.e.p.PluginsService     ] [nOyaNt9] loaded module [lang-expression]
[2017-08-16T09:52:00,254][INFO ][o.e.n.Node               ] initialized
[2017-08-16T09:52:00,254][INFO ][o.e.n.Node               ] [nOyaNt9] starting ...
[2017-08-16T09:52:00,462][INFO ][o.e.t.TransportService   ] [nOyaNt9] publish_address {172.17.0.2:9300}, bound_addresses {0.0.0.0:9300}
```
#### run-elasticsearch-d
```bash
docker run -d --name elk_e --restart=always -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xmx256m -Xms256m" \
        -v `pwd`/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
        dockerelk_elasticsearch
➜  docker-elk git:(master) ✗ docker run -d --name elk_e --restart=always -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xmx256m -Xms256m" \
        -v `pwd`/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
        dockerelk_elasticsearch
435d499e82fd6b691f507f49643802622976b35e7540de7d5b21819df8ec162d
➜  docker-elk git:(master) ✗ docker ps
CONTAINER ID        IMAGE                                     COMMAND                  CREATED             STATUS              PORTS                                            NAMES
435d499e82fd        dockerelk_elasticsearch                   "/bin/bash bin/es-..."   4 seconds ago       Up 3 seconds        0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp   elk_e
➜  docker-elk git:(master) ✗ docker logs -f elk_e
[2017-08-16T09:54:37,018][INFO ][o.e.n.Node               ] [] initializing ...
...
```
#### test-logstash
```bash
docker run --rm -it -p 5000:5000 --link elk_e:elasticsearch  -e LS_JAVA_OPTS="-Xmx256m -Xms256m" \
	-v `pwd`/logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml \
	-v `pwd`/logstash/pipeline:/usr/share/logstash/pipeline \
	 dockerelk_logstash


➜  docker-elk git:(master) ✗ docker run --rm -it -p 5000:5000 --link elk_e:elasticsearch  -e LS_JAVA_OPTS="-Xmx256m -Xms256m" \
        -v `pwd`/logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml \
        -v `pwd`/logstash/pipeline:/usr/share/logstash/pipeline \
         dockerelk_logstash
[2017-08-16T10:06:46,161][INFO ][logstash.setting.writabledirectory] Creating directory {:setting=>"path.queue", :path=>"/usr/share/logstash/data/queue"}
[2017-08-16T10:06:47,598][INFO ][logstash.pipeline        ] Pipeline main started
[2017-08-16T10:06:47,735][INFO ][logstash.agent           ] Successfully started Logstash API endpoint {:port=>9600}
```

#### run-logstash-d
```bash
docker run -d --name elk_l --restart=always -p 5000:5000 --link elk_e:elasticsearch  -e LS_JAVA_OPTS="-Xmx256m -Xms256m" \
        -v `pwd`/logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml \
        -v `pwd`/logstash/pipeline:/usr/share/logstash/pipeline  \
        dockerelk_logstash

```

#### test-kibana
```bash
docker run --rm -it -p 5601:5601 --link elk_e:elasticsearch \
	-v `pwd`/kibana/config/:/usr/share/kibana/config \
	dockerelk_kibana


➜  docker-elk git:(master) ✗ docker run --rm -it -p 5601:5601 --link elk_e:elasticsearch \
        -v `pwd`/kibana/config/:/usr/share/kibana/config \
        dockerelk_kibana
  log   [10:16:32.542] [info][optimize] Optimizing and caching bundles for kibana, stateSessionStorageRedirect, timelion and status_page. This may take a few minutes
```

#### run-kibana-d
```bash
docker run -d --name elk_k --restart=always -p 5601:5601 --link elk_e:elasticsearch  \
       -v `pwd`/kibana/config/:/usr/share/kibana/config  \
        dockerelk_kibana
➜  docker-elk git:(master) ✗ docker ps | grep elk
ea7a4b0a2981        dockerelk_kibana                          "/bin/sh -c /usr/l..."   4 seconds ago       Up 3 seconds        0.0.0.0:5601->5601/tcp                           elk_k
bb1c513224ec        dockerelk_logstash                        "/usr/local/bin/do..."   14 minutes ago      Up 14 minutes       5044/tcp, 0.0.0.0:5000->5000/tcp, 9600/tcp       elk_l
435d499e82fd        dockerelk_elasticsearch                   "/bin/bash bin/es-..."   28 minutes ago      Up 28 minutes       0.0.0.0:9200->9200/tcp, 0.0.0.0:9300->9300/tcp   elk_e
➜  docker-elk git:(master) ✗ docker logs -f elk_k
{"type":"log","@timestamp":"2017-08-16T10:23:03Z","tags":["info","optimize"],"pid":1,"message":"Optimizing and caching bundles for kibana, stateSessionStorageRedirect, timelion and status_page. This may take a few minutes"}

```
