# Python 베이스 이미지
FROM python:3.9-slim

# 작업 디렉토리 설정
WORKDIR /usr/src/app

# 필요한 시스템 패키지 설치
RUN apt-get clean && apt-get update && apt-get install -y \
    openjdk-17-jre-headless \
    procps \
    wget \
    curl \
    && apt-get clean

# JAVA_HOME 환경 변수 설정
ENV JAVA_HOME="/usr/lib/jvm/java-17-openjdk-amd64"
ENV PATH="$JAVA_HOME/bin:$PATH"

# Spark 및 Hadoop 의존성 설치
ENV SPARK_VERSION=3.3.2

#실행할때마다 오래걸려서 단축을 위해 로컬에 다운로드드
COPY spark-${SPARK_VERSION}-bin-hadoop3.tgz /tmp/
RUN tar -xzf /tmp/spark-${SPARK_VERSION}-bin-hadoop3.tgz && \
    mv spark-${SPARK_VERSION}-bin-hadoop3 /usr/local/spark && \
    rm -rf /tmp/spark-${SPARK_VERSION}-bin-hadoop3.tgz


# SPARK_HOME 및 PATH 설정
ENV SPARK_HOME="/usr/local/spark"
ENV PATH="$SPARK_HOME/bin:$PATH"

# PostgreSQL JDBC 드라이버 추가
RUN wget https://jdbc.postgresql.org/download/postgresql-42.5.4.jar -P $SPARK_HOME/jars/


RUN pip install apache-airflow-providers-apache-spark

# Python 의존성 설치
COPY docker/requirements.txt ./requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# 데이터 디렉토리 복사
COPY data/raw /usr/src/app/data/raw

# 환경 변수 파일 복사
COPY config/aws_credentials.env .env

# Airflow Spark Provider 추가 설치
RUN pip install apache-airflow-providers-apache-spark

# 모든 소스 코드 복사
COPY app/ .

# 기본 실행 명령어
CMD ["python", "spark_etl.py"]
