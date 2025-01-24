# Card_data_analysis

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



# 카드 데이터를 활용한 데이터 분석

## 📖 **프로젝트 개요**

이 프로젝트는 카드 소비 내역 데이터를 사용해 Elasticsearch, Kibana로 20대의 소비 패턴을 분석하고, 이를 기반으로 맞춤형 카드 상품을 설계하는 과정을 담고 있습니다. 가상의 카드사 직원 "김민준"의 관점에서 데이터를 분석하고, 실제 비즈니스 시나리오를 작성하여 카드사 고객들에게 최적화된 혜택을 제공하는 상품을 개발했습니다.


## 🎭 **시나리오: 가상의 카드사 직원 '김민준' 이야기**

### 1️⃣ **김민준의 목표**

- **직책**: 카드사의 데이터 분석 전문가
- **목표**: 20대 소비 데이터를 분석하여 소비가 가장 많은 카테고리에 최적화된 **맞춤형 카드 상품**을 출시.


### 2️⃣ **데이터 분석**

김민준은 카드 소비 데이터를 기반으로 20대의 소비 패턴을 분석했습니다. 주요 데이터 분석 결과는 다음과 같습니다:
![Image](https://github.com/user-attachments/assets/bea00741-ec40-484e-9411-27d26cc0abb9)
**20대 카드 사용 인원 수**

#### 🔍 **소비 데이터 요약**

- **분석 대상**: 20대, 755,000명
- **단위**: 소비 금액 (천 원)
- **카테고리별 소비 금액**:
![Image](https://github.com/user-attachments/assets/aa12b7af-0c79-48dc-8bc9-9cae71ab724f)

|**카테고리 그룹**|**합산 금액 (천 원)**|**합산 금액 (억 원)**|
|---|---|---|
|**요식업 관련**|68,465,700|684.6|
|**의류 및 신변잡화 관련**|4,038,030|40.3|
|**여행/레저/문화 관련**|20,074,330|200.7|
|**문화/취미 및 학습 관련**|1,198,540|11.9|

#### 🔑 **인사이트**

1. **요식업 소비가 가장 높음**:
    - 외식 및 휴게 음식점 소비가 전체 소비의 절반 이상을 차지.
2. **여행 및 레저 소비의 비중**:
    - 여행과 레저 활동은 두 번째로 높은 소비 항목으로, 20대의 여가 선호도를 반영.
3. **패션 및 의류 소비**:
    - 패션 관련 소비는 비교적 낮지만, 라이프스타일 변화에 따라 성장 가능성이 있음.


### 3️⃣ **상품 설계**

김민준은 분석 결과를 바탕으로 **20대 맞춤형 카드 상품**을 설계했습니다.

#### **카드 상품 1: 요식업 특화 카드**

- **주요 혜택**:
    - 프랜차이즈 음식점에서 **10% 할인**.
    - 일반 음식점에서도 **5% 캐시백** 제공.
- **목표 고객**: 외식 소비가 많은 20대.

#### **카드 상품 2: 여행/레저 특화 카드**

- **주요 혜택**:
    - 항공권 구매 시 **최대 15% 할인**.
    - 숙박 예약 플랫폼에서 **추가 적립 혜택**.
- **목표 고객**: 여행과 레저 활동을 즐기는 20대.

#### **카드 상품 3: 패션/의류 특화 카드**

- **주요 혜택**:
    - 패션 브랜드에서 **5~10% 할인**.
    - 신발 및 액세서리 구매 시 추가 포인트 적립.
- **목표 고객**: 의류와 신변잡화 소비 비중이 높은 고객.


### 4️⃣ **마케팅 및 실행 전략**

1. **타겟 설정**:
    - 20대, 특히 대학생과 사회초년생을 주요 타겟으로 설정.
2. **디지털 채널 활용**:
    - 모바일 앱과 SNS 광고를 통해 홍보.
    - 인플루언서 마케팅으로 젊은 층의 관심 유도.
3. **프로모션 캠페인**:
    - "20대 맞춤형 카드로 혜택을 누리세요!" 슬로건으로 첫 6개월 간 가입자 대상 추가 혜택 제공.

## 🚀 **프로젝트의 기대 효과**

1. **20대 고객 충성도 강화**:
    - 분석 기반으로 설계된 맞춤형 혜택 제공으로 고객 만족도 증가.
2. **매출 증대**:
    - 주요 소비 카테고리에서의 혜택으로 카드사 매출 상승.
3. **데이터 기반 비즈니스 모델**:
    - 소비 데이터를 활용한 성공 사례로 타 데이터 활용 프로젝트의 기반 마련.


## 📈 **데이터 분석 및 기술 사용**

- **분석 도구**: Elasticsearch, Kibana
- **시각화 방법**:
    - 소비 패턴 분석 결과를 바탕으로 Bar Chart 및 Pie Chart 생성.
    - 주요 소비 항목별 비율과 금액 비교.


## 💡 **향후 계획**

1. **소비 데이터 확장**:
    - 20대뿐만 아니라 30대, 40대까지 분석 범위를 확대.
2. **AI 기반 소비 추천 시스템 개발**:
    - 고객 소비 패턴에 맞춘 실시간 카드 혜택 추천.
3. **글로벌 시장 진출**:
    - 해외 소비 데이터를 활용한 글로벌 맞춤 카드 상품 설계.
