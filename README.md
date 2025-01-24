# NAT 네트워크 기반 가상 머신 환경에서 Elasticsearch와 Kibana 설치 하기

## 1. **환경 개요**🌐

### 시스템 아키텍처
![Image](https://github.com/user-attachments/assets/2d4ff498-507c-4035-80f8-3be41affa178)

### 가상 머신 설정:

- 운영체제: Ubuntu 24.04.01 LTS
- 메모리: 4GB
- 하드디스크: 20GB
- CPU: 2개
- 네트워크: NAT 네트워크 (포트 포워딩 설정)
- SSH: 활성화

### 서버 역할:

1. **Elasticsearch 서버**:
    - IP 주소: `10.0.2.4`
2. **Kibana 서버**:
    - IP 주소: `10.0.2.15`

### 포트 포워딩 설정:

- **Elasticsearch 서버**: 호스트의 `9200` 포트 → VM의 `9200` 포트
- **Kibana 서버**: 호스트의 `5601` 포트 → VM의 `5601` 포트
![Image](https://github.com/user-attachments/assets/158f67ab-3fd9-43db-af04-96d37f4ee5df)


## 2. 🔍**Elasticsearch 설치 및 설정**

### 2.1 Elasticsearch 다운로드 및 설치📥

1. **GPG 키 및 저장소 추가**
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
```
2. **Elasticsearch 설치**
```bash
sudo apt update
sudo apt install elasticsearch=7.11.1 -y
```
3. **Elasticsearch 서비스 시작 및 자동 시작 설정**
```bash
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```

### 2.2 `elasticsearch.yml` 파일 수정✏️

Elasticsearch가 외부에서 접속 가능하도록 설정 파일을 수정합니다.

1. **설정 파일 열기**
```bash
sudo vi /etc/elasticsearch/elasticsearch.yml
```
2. **다음 설정 추가 또는 수정**
```yaml
network.host: 0.0.0.0     # 모든 네트워크 인터페이스에서 접속 허용
http.port: 9200           # 기본 HTTP 포트
discovery.type: single-node  # 싱글 노드 환경 설정
```
3. **파일 저장 및 Elasticsearch 서비스 재시작**
```bash
sudo systemctl restart elasticsearch
```

### 2.3 Elasticsearch 접속 테스트✅

로컬 또는 외부에서 Elasticsearch에 접속하여 정상 작동 여부를 확인합니다.
### 내부 접속 테스트🌐
```bash
curl http://10.0.2.4:9200
```

### 정상 응답 예:
```json
{
  "name" : "myserver1",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "-1FK_dn7T2eUYFOZHbIcZQ",
  "version" : {
    "number" : "7.11.1",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "ff17057114c2199c9c1bbecc727003a907c0db7a",
    "build_date" : "2021-02-15T13:44:09.394032Z",
    "build_snapshot" : false,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

### 외부 접속 테스트🌐
1. **브라우저에서 Kibana 접속** 호스트 시스템에서 브라우저를 열고 다음 주소로 이동합니다:
```
http://localhost:9200
```
**결과 확인**
![Image](https://github.com/user-attachments/assets/58cbb486-bf65-466a-8bd4-96a6133072f3)
---

## 3. 📊**Kibana 설치 및 설정**

### 3.1 Kibana 다운로드 및 설치📥

1. **Kibana 설치** Elasticsearch와 동일한 저장소를 활용하여 Kibana를 설치합니다.
```bash
sudo apt update
sudo apt install kibana=7.11.1 -y
```
2. **Kibana 서비스 시작 및 자동 시작 설정**
```
sudo systemctl start kibana
sudo systemctl enable kibana
```

### 3.2 `kibana.yml` 파일 수정✏️

Kibana가 Elasticsearch 서버(`10.0.2.4`)와 연동되고, 외부에서 접속 가능하도록 설정합니다.

1. **설정 파일 열기**
```bash
sudo vi /etc/kibana/kibana.yml
```
2. **다음 설정 추가 또는 수정**
```yaml
# Elasticsearch 서버 주소
elasticsearch.hosts: ["http://10.0.2.4:9200"]

# Kibana 서버 외부 접속 허용
server.host: "0.0.0.0"

# Kibana 서버 포트
server.port: 5601
```
3. **파일 저장 및 Kibana 서비스 재시작**
```bash
sudo systemctl restart kibana
```

---

## 4. 🔗**Kibana 접속 및 확인**

1. **브라우저에서 Kibana 접속** 호스트 시스템에서 브라우저를 열고 다음 주소로 이동합니다:
```
http://localhost:5601
```
**결과 확인**
![Pasted image 20250124115813](https://github.com/user-attachments/assets/d4e58a1f-12b2-4c4b-a06f-df6d3c61b15b)
