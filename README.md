# 🚀 Spring Boot 애플리케이션 성능 테스트 프로젝트

## 👨‍👨‍👧‍👧 참여 인원 
| <img src="https://avatars.githubusercontent.com/u/83341978?v=4" width="150" height="150"/> | <img src="https://avatars.githubusercontent.com/u/129728196?v=4" width="150" height="150"/> | <img src="https://avatars.githubusercontent.com/u/104816148?v=4" width="150" height="150"/> |
|:-------------------------------------------------------------------------------------------:|:------------------------------------------------------------------------------------------:|:-------------------------------------------------------------------------------------------:|
|                     **박지원** <br/>[@jiione](https://github.com/jiione)                     |                      **최나영**<br/>[@na-rong](https://github.com/na-rong)                      |                     **박현서**<br/>[@hyleei](https://github.com/hyleei)                        |



## 📌 개요

Spring Boot 애플리케이션을 운영하면서 과부하나 지나친 요청으로 인한 장애를 방지하기 위해 **실시간 모니터링 도구**의 필요성을 느꼈습니다. 특히 가시성이 뛰어난 모니터링 툴을 도입하여 시스템 상태를 한눈에 파악하고자 했습니다. 이전에 ELK 스택을 활용해 Kibana로 대시보드를 시각화한 경험을 바탕으로, 이번에는 **Prometheus**와 **Grafana**를 사용하여 대시보드를 구성하기로 결정했습니다.

또한, 애플리케이션이 실제로 어느 정도의 부하를 견딜 수 있는지 파악하기 위해 **JMeter를 사용하여 다양한 요청 시나리오를 시뮬레이션**했습니다. 이를 통해 시스템의 안정성과 성능 한계를 검증하고, 잠재적인 문제점을 사전에 발견할 수 있었습니다.

이 프로젝트는 이러한 모니터링 및 부하 테스트 도구들을 통합하여 Spring Boot 애플리케이션의 성능을 최적화하고, 안정적인 운영 환경을 구축하는 것을 목표로 합니다.


## 🎯 기획

### 프로젝트 목표
- Spring Boot 애플리케이션의 성능 테스트 및 모니터링 환경 구축
- 부하 테스트를 통한 애플리케이션 성능 분석
- 실시간 메트릭 모니터링 시스템 구현

### 필요 도구
- Java JDK 17
- Spring Boot 3.3.4
- Apache JMeter 5.6.3
- Prometheus 2.30.3
- Grafana 11.2.2

### 테스트 시나리오
- 동시 사용자 수 증가에 따른 응답 시간 측정
- 장시간 부하 테스트를 통한 시스템 안정성 확인
- CPU, 메모리 사용량 모니터링

## 🛠 구성

### Spring Boot 애플리케이션 설정

1. 의존성 추가 (build.gradle):
```gradle
implementation 'org.springframework.boot:spring-boot-starter-actuator'
implementation 'io.micrometer:micrometer-registry-prometheus'
```

2. application.properties 설정:
```properties
management.endpoints.web.exposure.include=prometheus
management.endpoint.prometheus.enabled=true
server.port=8080
```

3. 애플리케이션 실행:
```bash
java -jar SpringApp-0.0.1-SNAPSHOT.jar
```

### JMeter 설정

1. JMeter 설치:
```bash
wget https://downloads.apache.org/jmeter/binaries/apache-jmeter-5.6.3.tgz
tar -xzf apache-jmeter-5.6.3.tgz
```

2. 테스트 계획 (test_plan.jmx) 생성:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<jmeterTestPlan version="1.2" properties="5.0" jmeter="5.5">
  <hashTree>
    <TestPlan guiclass="TestPlanGui" testclass="TestPlan" testname="Spring Boot App Stress Test Plan" enabled="true">
      <stringProp name="TestPlan.comments"></stringProp>
      <boolProp name="TestPlan.functional_mode">false</boolProp>
      <boolProp name="TestPlan.tearDown_on_shutdown">true</boolProp>
      <boolProp name="TestPlan.serialize_threadgroups">false</boolProp>
      <elementProp name="TestPlan.user_defined_variables" elementType="Arguments" guiclass="ArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
        <collectionProp name="Arguments.arguments"/>
      </elementProp>
      <stringProp name="TestPlan.user_define_classpath"></stringProp>
    </TestPlan>
    <hashTree>
      <ThreadGroup guiclass="ThreadGroupGui" testclass="ThreadGroup" testname="Stress Test Thread Group" enabled="true">
        <stringProp name="ThreadGroup.on_sample_error">continue</stringProp>
        <elementProp name="ThreadGroup.main_controller" elementType="LoopController" guiclass="LoopControlPanel" testclass="LoopController" testname="Loop Controller" enabled="true">
          <boolProp name="LoopController.continue_forever">false</boolProp>
          <stringProp name="LoopController.loops">100</stringProp>
        </elementProp>
        <stringProp name="ThreadGroup.num_threads">500</stringProp>
        <stringProp name="ThreadGroup.ramp_time">60</stringProp>
        <boolProp name="ThreadGroup.scheduler">false</boolProp>
        <stringProp name="ThreadGroup.duration"></stringProp>
        <stringProp name="ThreadGroup.delay"></stringProp>
        <boolProp name="ThreadGroup.same_user_on_next_iteration">true</boolProp>
      </ThreadGroup>
      <hashTree>
        <HTTPSamplerProxy guiclass="HttpTestSampleGui" testclass="HTTPSamplerProxy" testname="HTTP Request" enabled="true">
          <elementProp name="HTTPsampler.Arguments" elementType="Arguments" guiclass="HTTPArgumentsPanel" testclass="Arguments" testname="User Defined Variables" enabled="true">
            <collectionProp name="Arguments.arguments"/>
          </elementProp>
          <stringProp name="HTTPSampler.domain">localhost</stringProp>
          <stringProp name="HTTPSampler.port">8080</stringProp>
          <stringProp name="HTTPSampler.protocol">http</stringProp>
          <stringProp name="HTTPSampler.contentEncoding"></stringProp>
          <stringProp name="HTTPSampler.path">/test</stringProp>
          <stringProp name="HTTPSampler.method">GET</stringProp>
          <boolProp name="HTTPSampler.follow_redirects">true</boolProp>
          <boolProp name="HTTPSampler.auto_redirects">false</boolProp>
          <boolProp name="HTTPSampler.use_keepalive">true</boolProp>
          <boolProp name="HTTPSampler.DO_MULTIPART_POST">false</boolProp>
          <stringProp name="HTTPSampler.embedded_url_re"></stringProp>
          <stringProp name="HTTPSampler.connect_timeout"></stringProp>
          <stringProp name="HTTPSampler.response_timeout"></stringProp>
        </HTTPSamplerProxy>
        <hashTree/>
      </hashTree>
    </hashTree>
  </hashTree>
</jmeterTestPlan>
```

### Prometheus 설정

1. Prometheus 설치:
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.30.3/prometheus-2.30.3.linux-amd64.tar.gz
tar xvfz prometheus-*.tar.gz
```

2. prometheus.yml 설정:
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'spring_boot_app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['localhost:8080']
```

3. Prometheus 실행:
```bash
./prometheus --config.file=prometheus.yml
```

### Grafana 설정

1. Grafana 설치:
```bash
sudo apt-get install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install grafana
```

2. Grafana 서비스 시작:
```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

3. Grafana 대시보드 설정:
   - Prometheus 데이터 소스 추가
   - 대시보드 생성 (요청 처리량, 응답 시간, CPU 사용량, JVM 메모리 사용량 등)

## 🧪 테스트

### JMeter 과부하 테스트 실행

1. 테스트 계획 수정 (test_plan.jmx):
   - Thread Group 설정:
     - Number of Threads (users): 500
     - Ramp-up period (seconds): 60
     - Loop Count: 100

2. 과부하 테스트 실행:
```bash
jmeter -n -t test_plan.jmx -l results.jtl
```

3. 실시간 모니터링:
   - Grafana 대시보드에서 다음 메트릭 관찰:
     - 요청 처리량 (Throughput):
       ```
       rate(http_server_requests_seconds_count{job="spring_boot_app"}[1m])
       ```
     - CPU 사용량:
       ```
       system_cpu_usage{job="spring_boot_app"}
       ```
     - JVM 메모리 사용량:
       ```
       sum(jvm_memory_used_bytes{job="spring_boot_app"}) by (area)
       ```

## 📊 검증

### JMeter 결과 분석

```bash
jmeter -g results.jtl -o html_report
```
![image](https://github.com/user-attachments/assets/66b43697-5ae9-4e5d-b6c2-ec9f56443b0a)

### Prometheus 데이터 확인
웹 브라우저에서 `http://your_server_ip:9090` 접속

![image](https://github.com/user-attachments/assets/4e0f942d-9f69-478f-8fb0-ac2acab0036c)

### Grafana 대시보드 분석
- 요청 처리량, 응답 시간, 오류율 확인
- CPU, 메모리 사용량 추이 분석
- 병목 지점 식별 및 성능 개선 포인트 도출

![image](https://github.com/user-attachments/assets/543742b1-a784-4fa5-aac8-bfa0ec988246)



## 🔧 트러블슈팅

1. Spring Boot 애플리케이션 포트 문제
   - 문제: 애플리케이션이 8999 포트에서 실행되어 접근 불가
   - 해결: `application.properties`에서 `server.port=8080`으로 설정

2. Prometheus 타겟 접근 문제
   - 문제: Prometheus가 Spring Boot 애플리케이션 메트릭에 접근 불가
   - 해결: Spring Boot 애플리케이션의 Actuator 설정 확인 및 수정

3. Grafana 데이터 소스 연결 문제
   - 문제: Grafana에서 Prometheus 데이터 소스 연결 실패
   - 해결: Prometheus URL 및 접근 설정 확인

4. JMeter 실행 시 XStream 보안 예외
   - 문제: JMeter 실행 시 XStream 관련 보안 예외 발생
   - 해결: JMeter의 `user.properties` 파일에 `jmeter.save.saveservice.allowlist=*` 추가

5. 과도한 부하로 인한 시스템 불안정
   - 문제: 높은 부하 테스트 중 시스템 리소스 고갈
   - 해결: 테스트 계획의 스레드 수와 반복 횟수 조정, 서버 리소스 모니터링 강화

## 🏁 결론

이번 프로젝트를 통해 Spring Boot 애플리케이션의 **성능 최적화**에 있어 **부하 테스트와 모니터링**의 중요성을 재확인하였습니다. JMeter로 실제 사용 환경과 유사한 부하를 생성함으로써 잠재적인 문제점을 발견할 수 있었고, Prometheus와 Grafana를 통해 수집된 데이터로 명확한 개선 방향을 설정할 수 있었습니다. 이러한 과정은 **안정적이고 효율적인 애플리케이션 운영**을 위한 중요한 기반이 될 것입니다.

