## Model
# labshopgateway-20250612 ()




## 터미널 작성 참고용
1. kafka 폴더 내에 docker-compose.yml 버전 변경 -> 7.5.3

2. sdk install java

3. 각 모듈 내 lombok 1.18.30으로 수정
- monolith, inventory

4. httpie 설치
- pip install httpie

5. kafka 구동
- cd kafka
- docker compose up

6. monolith 마이크로서비스, gate way 구동
- cd monolith
- mvn spring-boot:run

- cd gateway
- mvn spring-boot:run

7. 주문 1건 요청 (order 서비스와 gateway 서버 비교 테스트)
- http localhost:8081/orders productId=1 qty=3 customerId=1001
- http localhost:8081/orders

- http localhost:8088/orders productId=1 qty=1 customerId=1001
- http localhost:8081/orders 
- http localhost:8088/orders

8. inventory 마이크로 서비스 실행
- 게이트웨이서비스의 application.yaml 의 spring.cloud.gateway.routes 에 아래 설정을 추가하여 
- inventory 서비스로의 라우팅을 추가한다. (indent 에 주의해주세요)
-      - id: inventory
-        uri: http://localhost:8082
-        predicates:
-          - Path=/inventories/**

- http localhost:8082/inventories
- http localhost:8088/inventories

9. inventory 실행 및 테스트
- cd inventory
- mvn spring-boot:run

- http localhost:8082/inventories
- http localhost:8088/inventories
---
## Before Running Services
### Make sure there is a Kafka server running
```
cd kafka
docker-compose up
```
- Check the Kafka messages:
```
cd infra
docker-compose exec -it kafka /bin/bash
cd /bin
./kafka-console-consumer --bootstrap-server localhost:9092 --topic
```

## Run the backend micro-services
See the README.md files inside the each microservices directory:

- order
- inventory


## Run API Gateway (Spring Gateway)
```
cd gateway
mvn spring-boot:run
```

## Test by API
- order
```
 http :8088/orders id="id" productId="productId" qty="qty" customerId="customerId" amount="amount" 
```
- inventory
```
 http :8088/inventories id="id" stock="stock" 
```


## Run the frontend
```
cd frontend
npm i
npm run serve
```

## Test by UI
Open a browser to localhost:8088

## Required Utilities

- httpie (alternative for curl / POSTMAN) and network utils
```
sudo apt-get update
sudo apt-get install net-tools
sudo apt install iputils-ping
pip install httpie
```

- kubernetes utilities (kubectl)
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

- aws cli (aws)
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

- eksctl 
```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
```

