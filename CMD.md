# CMD

## cluster  
### 后端服务   
node recipe-api/producer-http-basic-master.js


## 日志  
### 带日志的前端服务  
NODE_ENV=development LOGSTASH=localhost:7777 node web-api/consumer-http-logs.js

### ELK  
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -p 7777:7777/udp -v $PWD/misc/elk/udp.conf:/etc/logstash/conf.d/99-input-udp.conf -e MAX_MAP_COUNT=262144 -it --name distnode-elk sebp/elk:683


## Graphite, StatsD, Grafana  
docker run -p 8080:80 -p 8125:8125/udp -it --name distnode-graphite graphiteapp/graphite-statsd:1.1.6-1
docker run -p 8000:3000 -it --name distnode-grafana grafana/grafana:6.5.2

### 带度量指标的前端服务  
NODE_DEBUG=statsd-client node web-api/consumer-http-metrics.js
autocannon -d 300 -R 5 -c 1 http://localhost:3000
curl http://localhost:3000/error


## 请求追踪  
### zipkin 请求追踪  
docker run -p 9411:9411 -it --name distnode-zipkin openzipkin/zipkin-slim:2.19

### 带zipkin的前端服务  
node recipe-api/producer-http-zipkin.js
node web-api/consumer-http-zipkin.js
curl http://localhost:3000/


## postgres, redis  
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=hunter2 -e POSTGRES_USER=tmp -e POSTGRES_DB=tmp postgres:12.3
docker run --rm -p 6379:6379 redis:6.0

### 带健康检查的前端服务  
PGUSER=tmp PGPASSWORD=hunter2 PGDATABASE=tmp node basic-http-healthcheck.js
curl -v http://localhost:3300/health

## 容器  
### Docker  
docker run -it --rm --name ephemeral ubuntu /bin/bash
docker ps
docker exec ephemeral /bin/ls /var

curl -o index.html https://www.163.com
docker run --rm -p 8080:80 -v $PWD/:/usr/share/nginx/html nginx

docker build -t alan01/recipe-api:v0.0.1 .
docker history alan01/recipe-api:v0.0.1

docker-compose up


## 容器编排
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.10
kubectl get deployments
kubectl get pods
kubectl get rs

kubectl expose deployment hello-minikube --type=NodePort --port=8080
kubectl get services -o wide

minikube service hello-minikube --url
curl `minikube service hello-minikube --url`


## 弹性
docker run --name distnode-memcached -p 11211:11211 -it --rm memcached:1.6-alpine memcached -m 64 -vv

docker run --name distnode-postgres -it --rm -p 5432:5432 -e POSTGRES_PASSWORD=hunter2 -e POSTGRES_USER=user -e POSTGRES_DB=dbconn postgres:12.3

