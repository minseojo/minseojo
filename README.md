# Minseo Jo — Real-world Data Platform Engineer

> **물리 세계에서 생성되는 대규모 데이터(위성, 공간, 시계열)를 처리하는 분산 플랫폼을 설계하는게 목표입니다.**<br>
> 공간 데이터(GIS)를 거쳐, 현재는 위성 지상 시스템(Ground Segment)을 개발하고 있습니다.

`Distributed Systems` `Data Platform` `Event-Driven Architecture` `Spatial / Time-series Data`

- **나라스페이스테크놀로지** — 지상소프트웨어 & 데이터플랫폼팀 (2026.07 ~ 현재)
- **아이씨티웨이(ICTWAY)** — 3D GIS 엔진 & 백엔드 엔지니어 (2024.12 ~ 2026.07)
- **전북대학교** IT정보공학과 학사 (2019.03 ~ 2025.02)
- **소프트웨어 마에스트로 15기** 백엔드 리드 (2024.04 ~ 2024.11)

---

## 개발 원칙

- **문제를 기술로 풀지, 프로세스·커뮤니케이션으로 풀지부터 판단한다.** 기술이 당연한 해결책이라고 전제하지 않고, 여러 선택지 중 하나로 놓고 검토한다.
- **기술로 풀기로 했다면, 가장 간단한 해결책을 먼저 선택한다.** 문제 크기에 비해 과한 설계를 경계한다.
- **목표를 마일스톤으로 쪼개 하나씩 해결한다.** 큰 문제는 검증 가능한 단위로 나누고, 단계마다 결과를 확인하며 진행한다.
- **실패를 전제로 설계한다.** 네트워크 단절, 프로세스 중단, 메시지 중복은 항상 발생한다고 가정하고 복구 가능한 구조를 만든다.
- **성능은 수치로 검증한다.** 개선 전후를 같은 조건에서 측정하고, 재현 가능한 수치로 판단한다.

---

## 주요 프로젝트

### 1. Kafka 기반 이벤트 주도 분산 빌드 파이프라인 `2025.06 ~ 2026.02`
- **문제** — NFS 위 단일 SQLite 파일을 다중 서버가 공유하며 발생한 I/O 락 경합, 수평 확장이 불가능한 모놀리식 빌더.
- **해결** — CQRS 기반 저장소 분리(PostgreSQL + 분산 오브젝트 스토리지), Kafka Consumer Group 분산 처리, Transactional Outbox 패턴으로 상태 변경과 이벤트 발행의 원자성 확보.
- **성과** — 분산 환경 데이터 정합성 **100% 보장**, 워커 노드 증설 시 **약 90% 선형 수평 확장** 효율 달성.

### 2. CAS(Content Addressable Storage) 기반 시공간 증분 빌드 & Git-like 버전 관리 시스템 `2025.06 ~ 2026.02`
- **문제** — 일부 영역 수정에도 전국 단위 전체 재빌드(수백 시간)가 필요하고, 데이터 이력 관리가 부재.
- **해결** — Git 객체 모델을 참고한 SHA-1 기반 **Content-Addressable Storage** + **Merkle Tree** 설계, 변경된 영역만 감지해 영향받는 데이터만 재생성하는 증분 빌드, 포인터 교체 방식의 무중단 배포 구현.
- **성과** — 재빌드 **수백 시간 → 수 분**, 배포 전환 **ms 단위**, 중복 제거로 스토리지 비용 **40~90% 절감**.

### 3. 공간 인덱스 재설계 — 트리 탐색을 좌표 계산으로 대체 `2025.03 ~ 2025.06`
- **문제** — 수백만 개 데이터 조각(타일)의 위치를 쿼드트리 순회로 탐색($$O(\log_4 N)$$)하고, 트리 전체 메타데이터를 클라이언트가 통째로 내려받아야 하는 구조.
- **해결** — 트리 자료구조를 규칙 기반 인코딩으로 대체. Z-Order(Morton) 인덱싱으로 좌표에서 데이터 위치를 직접 계산하는 **$$O(1)$$ 랜덤 액세스** 구현, 메타데이터는 서브트리 단위로 분할해 필요한 부분만 로드.
- **성과** — 렌더링 FPS **약 30% 향상**, 클라이언트 메모리 점유 **수백 MB → 수십 KB**, 메타데이터 용량 **30~40% 절감**.
- **블로그** — [전 세계 공간정보를 다루는 자료구조 구현기](https://virtualworld.tistory.com/23)

### 4. 대용량 바이너리 서빙 API 최적화 `2026.01 ~ 2026.04`
- **문제** — 최대 20MB 단위의 대용량 응답을 동기 방식으로 서빙하며 발생한 I/O 병목, WAS 스레드 고갈 및 JVM 메모리 불안정.
- **해결** — StreamingResponseBody 비동기 청크 전송, Caffeine 캐시, HTTP/2 멀티플렉싱 도입.
- **성과** — TTFB **27% 개선**, 스레드 고갈과 OOM 방어로 서빙 안정성 확보.

### 5. 바이너리 직접 파싱을 통한 포맷 변환 가속 `2025.05 ~ 2025.06`
- **문제** — 3D 데이터 포맷 변환 시 외부 라이브러리가 파일 전체를 디코딩 후 재인코딩하는 오버헤드.
- **해결** — 포맷의 바이너리 명세를 직접 파싱, 헤더 슬라이싱으로 필요한 영역만 추출해 재인코딩 단계 제거.
- **성과** — 2GB(3,500개 파일) 기준 변환 **20분 → 5초, 약 240배 가속**.

---

## 학부 프로젝트

### 6. Text-to-Image AI 네 컷 만화 서비스 — 소프트웨어 마에스트로 15기 `2024.04 ~ 2024.11`
- **문제** — 한 장당 수 초 이상 걸리는 이미지 생성 요청 폭주 시 동기식 WAS 스레드 풀 고갈, 고비용 GPU 인스턴스 상시 운영 부담.
- **해결** — AWS SQS로 요청 레이어(CPU)와 생성 레이어(GPU)를 비동기 분리, CloudWatch 지표 기반 Spot Instance 오토스케일링 및 롤링 배포 설계.
- **성과** — 요청 폭주 상황에서도 API 서버 **무중단 유지**, 인프라 비용 **56% 절감**.

### 7. MySQL 샤딩 vs 인덱싱 정량 비교 연구 — 졸업 프로젝트 `2024.03 ~ 2024.06`
- **문제** — 대용량 테이블 확장 전략(샤딩 vs 인덱싱) 선택에 대한 정량적 판단 근거 부재.
- **해결** — 960만 건 데이터셋을 대상으로 쿼리 유형 × 인덱스 조합별(풀 테이블 / 레인지 스캔 / 커버링 인덱스) 부하를 통제한 비교 실험.
- **성과** — **"선 인덱싱 최적화, 후 물리 샤딩"** 의사결정 기준 수립.

---

## 기술 스택

| 분류 | 주력 | 학습 중 |
| :--- | :--- | :--- |
| **언어** | Java, C#, Python | |
| **백엔드** | Spring Framework, FastAPI, JPA/Hibernate | OAuth2/OIDC |
| **데이터 & 메시징** | PostgreSQL (PostGIS), MySQL, Apache Kafka | |
| **인프라 & DevOps** | AWS, Docker, GitHub Actions | Kubernetes |
| **모니터링** | Grafana, Prometheus, k6 | |

---

## 블로그
- **[네트워크]** [Dual Write와 데이터 정합성 — 전파 지연과 장애 복구 전략](https://virtualworld.tistory.com/53)
- **[네트워크]** [신뢰성을 위한 Timeout 처리와 멱등성 설계 전략](https://virtualworld.tistory.com/44)
- **[I/O 최적화]** [대용량 파일 멀티파트 업로드, 정말 빠를까?](https://virtualworld.tistory.com/62)
- **[백엔드]** [Spring 비동기 처리 — MVC 비동기 → WebFlux → Virtual Threads](https://virtualworld.tistory.com/54)
- **[분산 시스템]** [5000개 파일을 원자적으로 배포하기 — Atomic Rename 패턴과 SeaweedFS 아키텍처](https://virtualworld.tistory.com/45)
- **[데이터베이스]** [PostgreSQL 내부구조 시리즈](https://virtualworld.tistory.com/63)
- **[데이터베이스]** [Log-based System — RDBMS와 Kafka 구조 분석 — WAL vs Commit Log](https://virtualworld.tistory.com/19)
- **[운영체제]** [Zero-Copy — 파일 전송 최적화와 Kafka 코드 분석](https://virtualworld.tistory.com/20)

---

## 연락처

- 이메일: [liging12@naver.com](mailto:liging12@naver.com)
- 링크드인: [linkedin.com/in/minseojo](https://www.linkedin.com/in/minseojo/)
- 블로그: [virtualworld.tistory.com](https://virtualworld.tistory.com/)
