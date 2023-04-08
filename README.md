# Elastic Search Hands-on
ElasticSearch 는 2004년 Shay Banon 라는 인물이 Apache Lucene 보완을 계기로 시작된 **검색엔진**이다.
현재는 `Logstash`, `Kibana` 프로젝트를 정식 흡수하여 Elastic Stack 을 함께 개발해 나가고 있다. 

ElasticSearch 는 뛰어난 검색 능력과 대규모 분산 시스템, 쉬운 설치 및 사용 등.. 의 장점을 가지고 있다.
또한 실시간 분석이 가능하기 때문에 대용량 데이터 분석에도 널리 쓰인다.

`Full Text` 검색 엔진이며 역파일 색인 구조로 저장된다. 
또한 key-value 형식이 아닌 문서 기반으로 복합적인 정보를 포함하는 형식의 문서를 있는 그대로 저장 가능하다.

이 외에도 RESTFul API 를 통해 데이터 조회, 입력, 삭제 등..을 처리하며, 멀티테넌시 환경을 가지고 있다.
Index 라는 논리적인 집합 단위로 구성되어 분산 저장되기 때문에 별도의 커넥션 없이 하나의 질의로 묶어 검색하고, 하나의 출력으로 도출 할 수 있다.

## Install with Docker
ElasticSearch Docker 이미지는 [공식 홈페이지](https://www.docker.elastic.co/r/elasticsearch)에서 배포하고 있으며 
[가이드](https://github.com/elastic/elasticsearch)도 제공해주고 있다. 
또한 오픈소스 이기 때문에 [Github](https://github.com/elastic/elasticsearch)을 통해서 소스코드도 볼 수 있다.

``` shell
> docker pull docker.elastic.co/elasticsearch/elasticsearch:<버전>
> docker network create elastic
> docker run --name es01 --net elastic -p 9200:9200 -p 9300:9300 -it docker.elastic.co/elasticsearch/elasticsearch:<버전>
```

ElasticSearch 를 실행하면 아래와 같이 인증서 파일이 생성된다. 인증서를 추출하여 아래와 같이 curl 을 통해 요청을 보낼 수 있다.
``` shell
> docker cp <컨테이너 이름>:/usr/share/elasticsearch/config/certs/http_ca.crt . --name <유저 이름>
> curl --cacert http_ca.crt -u <유저 이름> https://localhost:9200
```

> 위와 같이 curl 을 통해 요청을 보내면 패스워드를 요구한다. 이는 아래 명령을 통해서 패스워드 재설정이 가능하다.
> ``` shell 
> > docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-reset-password --username elastic  
> ```

`ES_JAVA_OPTS` option 을 통해, `-Xms`와 `-Xmx`를 설정하여 JVM Heep 크기를 지정해 줄 수 있다.
```
docker run -e ES_JAVA_OPTS="-Xms1g -Xmx1g" ...
```

## CRUD
위에서 언급함과 같이 ElasticSearch 는 RESTful, JSON 으로 정보를 주고 받는다. 또한 저장하는 방식도 JSON 방식으로 저장한다.   

> Elastic Stack 중 Kibana 는 Postman 과 같이 Rest API 를 쉽게 실행 할 수 있는 Dev Tools 라는 도구를 제공한다.
> ```shell
> > docker pull docker.elastic.co/kibana/kibana:8.7.0
> > docker run --name kib-01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:<버전>
> 
> ...
> 
> i Kibana has not been configured.
> 
> Go to http://0.0.0.0:5601/?code=247418 to get started.
> 
> Your verification code is:  247 418
> ```
> 실행 후 log 로 나타난 url 로 접속하여 초기 세팅 후 Kibana 를 사용 할 수 있다. 
> 초기 세팅에서 Cluster 의 Enrollment Token 과 ElasticSearch 의 계정을 입력하여야한다.
> 이는 ElasticSearch 실행 시 나타나는 log 에서 확인 할 수 있으며, 
> `bin/elasticsearch-reset-password` 또는 `bin/elasticsearch-create-enrollment-token` 를 통해 재성성 및 재설정이 가능하다.   
> 
> 만약 초기 설정까지 완료하였다면 왼쪽 메뉴 중 Management > Dev Tools 로 이동하여 사용이 가능하다.

