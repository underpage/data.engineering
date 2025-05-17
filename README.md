# Data Engineering
: 데이터 수집, 저장, 처리, 배포 등 파이프라인 구성

**구성 요소**

요소 | 설명
---|---
Python  | 파이프라인 구현 및 제어
Airflow | 작업 관리 (스케줄링, 모니터링, 실패처리)
Kafka   | 데이터 수신 및 메시지 전달
Spark   | 대용량 데이터 처리 (배치, 실시간)
Flink   | 실시간 데이터 처리
Hadoop  | 대용량 데이터 처리 및 분산 저장
MySQL   | 정형 데이터 저장
MongoDB | 비정형 데이터 저장
DataHub | 데이터 카탈로그


**흐름**
```
Producer ==> Kafka ==> Spark | Flink ==> Hadoop | MySQL | MongoDB
```

1. Producer가 다양한 소스에서 데이터를 수집해 Kafka로 전송
2. Kafka가 데이터를 수신해 일시 저장하고 Spark 또는 Flink에 전달
3. Spark 또는 Flink가 데이터를 처리해 Hadoop(HDFS)에 저장
4. 각 단계에서 메타데이터가 DataHub로 전송됨
5. Airflow가 전체 과정을 모니터링하고 실패를 처리함
6. Airflow가 주기적으로 Hadoop에 저장된 데이터에서 필요한 정보를 추출해 DBMS에 적재
7. DataHub가 전체 데이터 자산을 카탈로그화함


**구성**
```bash
project/
├── config
├── containers      # 컨테이너 설정 파일들
│   ├── airflow
│   ├── atlas
│   ├── hadoop
│   ├── kafka
│   └── spark
├── dags            # Airflow 워크플로우 정의
├── scripts
├── src             # 파이썬 기반 처리 로직
│   ├── ingestion   # 데이터 수집
│   ├── metadata    # DataHub
│   ├── monitoring  # 데이터 품질
│   ├── processing  # Spark, Flink
│   ├── storage     # HDFS, MySQL, MongoDB
│   └── utils
├── test
├── .env
├── environment.yml
└── README.yml
```



## Python

```bash
# 가상 환경 생성
conda create -n example python=3.11

# 가상 환경 활성화
conda activate example

# 프로젝트 생성
mkdir ~/example && cd ~/example

# 모듈 설치
pip install python-dotenv cryptography great_expectations pytest fastapi uvicorn \
httpx aiokafka pyspark apache-airflow hdfs pyarrow motor aiomysql acryl-datahub

## 설정 파일 생성
conda env export > environment.yml

## 설정 파일 적용
conda env create -f environment.yml
```


**설치 모듈**

모듈 | 설명
---|---
python-dotenv | 환경 변수 로드 도구
cryptography  | 암호화 및 보안 기능 제공 라이브러리
great_expectations | 데이터 품질 검사 및 유효성 검증 도구
pytest   | 파이썬 테스트 프레임워크
fastapi  | API 프레임워크
httpx    | 비동기를 위한 HTTP 클라이언트
acryl-datahub | 메타데이터 관리 도구 