<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>

## 미션

* 미션 진행 후에 아래 질문의 답을 작성하여 PR을 보내주세요.

### 1단계 - 인프라 운영하기
1. 각 서버내 로깅 경로를 알려주세요
- ec2 instance id: `i-0d1b65e7205412982`
  - log group name: `kelicia91`
    - `/var/log/syslog`
    - `/var/log/nginx`

2. Cloudwatch 대시보드 URL을 알려주세요
- https://ap-northeast-2.console.aws.amazon.com/cloudwatch/home?region=ap-northeast-2#dashboards:name=kelicia91-dashboard

---

### 2단계 - 성능 테스트
#### 1. 웹 성능 예산은 어느 정도가 적당하다고 생각하시나요

* 서비스 진입점인 `메인 페이지`를 기준으로 비교.

| 비교 분석 | [내 사이트](https://kelicia91.kro.kr/) | [네이버지도](https://map.naver.com/v5/subway) | [카카오맵](https://map.kakao.com/) |
|:------:|:------:|:------:|:------:|
|First Contentful Paint   |2.8s|2.5s|1.6s|
|Speed Index              |2.8s|3.4s|2.7s|
|Largest Contentful Paint |2.9s|3.5s|2.8s|
|Time to Interactive      |2.9s|3.1s|3.2s|
|Total Blocking Time      |≥ 0.050s|≥ 0.580s|≥ 0.680s|
|Cumulative Layout Shift  |0.004|0.019|0.018|

* 웹 성능 예산 추산 (성능 점수가 낮은 항목만 고려)
  - First Contentful Paint : 2.0s
  - Speed Index : 2.7s
  - Largest Contentful Paint : 2.8s
  - Time to Interactive : 2.8s

#### 2. 웹 성능 예산을 바탕으로 현재 지하철 노선도 서비스는 어떤 부분을 개선하면 좋을까요
- 텍스트 압축 사용
  - `application.properties` 파일에서 `server.compression` 설정 후 측정 결과
    - First Contentful Paint : 1.3s
    - Speed Index : 1.7s
    - Largest Contentful Paint : 1.4s
    - Time to Interactive : 1.4s
- 미사용 자바스크립트 줄이기
- 렌더링 차단 리소스 제거하기
- 정적 리소스 캐싱

#### 3. 부하테스트 전제조건은 어느 정도로 설정하셨나요
1. 예상 1일 사용자 수(DAU) : 1,500,000
2. 예상 피크 시간대의 집중률(최대 트래픽 / 평소 트래픽) : 3
3. 예상 1명당 1일 평균 접속 혹은 요청수 : 4
4. Throughput (일반적으로 Latency는 50~100ms 이하)
   - 1일 총 접속 수 = 1,500,000 * 4 = 6,000,000
   - 1일 평균 rps = 6,000,000 / 86,400 (초/일) = 약 69
   - 1일 최대 rps = 69 * 3 = 207

#### 4. Smoke, Load, Stress 테스트 스크립트와 결과를 공유해주세요
- 시나리오
  1. 접속 빈도가 높은 페이지 - 메인
     - 스크립트, 결과
       - [smoke.js](/k6/frequent/smoke.js), [smoke.log](/k6/frequent/smoke.log)
       - [load.js](/k6/frequent/load.js), [load.log](/k6/frequent/load.log)
       - [stress.js](/k6/frequent/stress.js), [stress.log](/k6/frequent/stress.log)
  2. 데이터를 갱신하는 페이지 - 로그인 후 나의 정보 수정
      - 스크립트, 결과
          - [smoke.js](/k6/edit/smoke.js), [smoke.log](/k6/edit/smoke.log)
          - [load.js](/k6/edit/load.js), [load.log](/k6/edit/load.log)
          - [stress.js](/k6/edit/stress.js), [stress.log](/k6/edit/stress.log)
  3. 데이터를 조회하는데 여러 데이터를 참조하는 페이지 - 경로 검색
      - 스크립트, 결과
          - [smoke.js](/k6/references/smoke.js), [smoke.log](/k6/references/smoke.log)
          - [load.js](/k6/references/load.js), [load.log](/k6/references/load.log)
          - [stress.js](/k6/references/stress.js), [stress.log](/k6/references/stress.log)
