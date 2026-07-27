# Minseo Jo — Spatial Data Infrastructure Engineer

> 대규모 공간 데이터를 위한 분산 시스템과 성능 최적화를 다뤄왔습니다. <br>
> 3D GIS 엔진의 수집–색인–빌드–서빙 전 구간을 고도화하고, 현재는 초소형위성 지상국 소프트웨어와 데이터 인프라를 개발하고 있습니다.

`Satellite Ground Segment` `Spatial Indexing` `Distributed Systems` `Large-scale Data Pipeline` `Binary Protocols`

- **나라스페이스테크놀로지** — 지상소프트웨어 & 데이터플랫폼팀 (2026.07 ~ 현재)
- **아이씨티웨이** — 3D GIS 엔진 & 백엔드 엔지니어 (2024.12 ~ 2026.06)
- **전북대학교** IT정보공학과 학사 (2019.03 ~ 2025.02)
- **소프트웨어 마에스트로 15기** 백엔드 리드 (2024.04 ~ 2024.11)

---

## 주요 프로젝트

### 1. 스트리밍 기반 대용량 래스터 COG 적재 파이프라인 `2026.05 ~ 2026.06`

- **문제** — 수 GB급 원본 래스터(항공·위성 영상)를 서버에서 통째로 변환하면 원본 크기에 비례한 메모리·디스크를 점유하고, 임의 바이트 분할 업로드는 조립 시 픽셀 재인코딩(화질 손실 위험)을 강제.
- **해결** — 업로드 단위를 최종 COG 내부 블록과 1:1로 일치시키는 역설계. 브라우저(gdal3.js WASM) 선타일링 + 서버 무재인코딩 byte-copy 조립, BigTIFF 헤더·IFD 순수 Java 직렬화, 파일 존재 자체가 진행 상태가 되는 격자 좌표 단위 재개 프로토콜, SHA-256 3구간 무결성 체인.
- **성과** — **원본이 수십 GB로 커져도 조립 피크 메모리 평탄(원본 크기와 무관) 실측**, 업로드 중단 시 미수신 블록만 재전송해 재전송량 절감, 조립 결과의 픽셀 체크섬 원본과 완전 일치(무손실).

### 2. Z-Order 기반 O(1) 공간 인덱싱 및 대규모 타일 서빙 구조 설계 `2025.03 ~ 2025.06`

- **문제** — 수십만 개 공간 타일의 위치를 쿼드트리 순회로 탐색($$O(\log_4 N)$$)하고, 수 GB 단일 JSON 메타데이터를 클라이언트가 통째로 내려받아야 하는 구조.
- **해결** — 계산으로 유도할 수 있는 관계(타일 주소)를 데이터로 저장하지 않는다. Z-Order(Morton) 인덱싱으로 좌표에서 타일 주소를 직접 계산하는 **$$O(1)$$ 랜덤 액세스** 구현, 존재 여부는 서브트리 단위 비트마스크로 분할해 필요한 부분만 로드.
- **성과** — 렌더링 FPS **약 30% 향상**, 클라이언트 메모리 **수백 MB → 수십 KB**, 좌표→타일 역계산 확보로 증분 빌드(4번)의 기반 마련.
- **블로그** — [전 세계 공간정보를 다루는 자료구조 구현기](https://virtualworld.tistory.com/23)

### 3. Kafka 기반 이벤트 주도 분산 빌드 파이프라인 `2025.06 ~ 2026.02`

- **문제** — NFS 위 단일 SQLite 파일을 다중 서버가 공유하며 발생한 파일 락 직렬화. 서버를 늘려도 처리량이 늘지 않는 모놀리식 빌더.
- **해결** — 저장소 분리(PostgreSQL + 오브젝트 스토리지), Kafka Consumer Group 분산 처리, Transactional Outbox로 Dual Write 차단, DB 상태 머신 + 멱등키로 Effectively-Once, 락 없는 원자적 작업 선점(`UPDATE WHERE status` + Affected Rows).
- **성과** — 워커 **2대 1.8배 · 3대 2.7배 · 5대 4.5배** 처리량(대당 효율 90%, 선형 확장 실측), 분산 정합성 사고 **0건**.
- **블로그** — [Dual Write와 데이터 정합성 — 전파 지연과 장애 복구 전략](https://virtualworld.tistory.com/53)

### 4. Git 객체 모델 기반 증분 빌드 시스템 `2025.06 ~ 2026.02`

- **문제** — 일부 영역 수정에도 전국 단위 전체 재빌드(수백 시간)와 전체 복제가 필요. 스토리지 비용이 '데이터 양 × 버전 수'로 선형 증가.
- **해결** — 타일 주소를 위치(좌표)에서 내용(SHA-1 해시)으로 역전. Git 객체 모델을 참고한 **Content-Addressable Storage** + 2단계 스냅샷으로 변경 타일만 자동 식별·생성, 단일 트랜잭션 포인터 교체로 무중단 배포·롤백.
- **성과** — 증분 빌드 전환, 배포·롤백 **데이터 크기 무관 ms 단위**, 중복 제거로 스토리지 비용 **40~90% 절감**.
- **블로그** — [5,000개 파일을 원자적으로 배포하기 — Atomic Rename 패턴과 SeaweedFS](https://virtualworld.tistory.com/45)

### 5. Spring 4 → Boot 3.4 마이그레이션 및 비동기 스트리밍 서빙 최적화 `2025.12 ~ 2026.02`

- **문제** — Spring 4(Java 8) 레거시 + 최대 20MB 타일을 `byte[]`로 통째 적재 후 응답하는 동기 서빙 — GC 부하, OOM 위험, HTTP/1.1 커넥션 경합.
- **해결** — Spring Boot 3.4 · Java 17 마이그레이션 전면 주도(WAR→JAR, Maven→Gradle, G1GC), StreamingResponseBody 비동기 청크 스트리밍, Caffeine 캐시, HTTP/2 멀티플렉싱.
- **성과** — TTFB **27% 개선**, 힙 점유를 파일 크기 무관 버퍼 수준으로 안정화, 빌드 시간 **50~70% 단축**.
- **블로그** — [Spring 4 → Boot 3.4 마이그레이션 — Java 17, Gradle, JAR, 그리고 제어권의 변화](https://virtualworld.tistory.com/48)
- **블로그** — [Spring 비동기 처리 — MVC 비동기 → WebFlux → Virtual Threads](https://virtualworld.tistory.com/54)

### 6. 바이너리 직접 파싱 기반 B3DM → glTF 2.0 포맷 변환 `2025.05 ~ 2025.06`

- **문제** — B3DM→glTF 2.0 변환 시 외부 도구가 파일마다 압축 해제→역직렬화→재직렬화→재압축 사이클을 수행 — 3,500개 파일(2GB)에 20분.
- **해결** — B3DM 28바이트 고정 헤더에서 GLB 오프셋·크기를 산술로 계산, 압축 상태 그대로 바이트 슬라이싱. glTF 2.0 직렬화(4바이트 정렬·패딩) 직접 구현.
- **성과** — 변환 **20분 → 5초, 약 240배 가속**, 외부 의존성 제거.
- **블로그** — [3D Tiles 1.0 — B3DM](https://virtualworld.tistory.com/17)
- **블로그** — [3D Tiles 1.1 — glTF 2.0](https://virtualworld.tistory.com/16)

### 7. LiDAR 포인트 클라우드 기반 HD Map 차선 자동 추출 `2026.02 ~ 2026.06`

- **문제** — MMS 측량 LiDAR에서 자율주행 HD Map 도로 요소(차선·노면 표시)를 수작업 도화로 추출
- **해결** — 단계별 제어·교체 가능한 파이프라인(전처리→6채널 BEV 래스터화→**SegFormer-B5**→벡터화)으로 전환. 99:1 클래스 불균형을 OHEM + Focal·Dice 융합 loss + 라벨 dilation으로 대응, 픽셀 마스크→Shapefile(EPSG:5186) 벡터화.
- **성과** — mIoU 차선 **0.78** · 노면 표시 **0.81** (초기 0.14 수준에서 개선), 전 단계 단독 설계·구현.

---

## 학부 프로젝트

### 8. Text-to-Image AI 네 컷 만화 서비스 — SW 마에스트로 15기 `2024.04 ~ 2024.11`

- CPU(API)·GPU(AI 워커)를 SQS로 분리하고 Spot Instance 자동 생성·반납 — GPU 비용 **56% 절감**, 메시지 유실 **0%**, 피드 응답 **46% 개선**.

### 9. MySQL 샤딩 vs 인덱싱 정량 비교 — 졸업 프로젝트 `2024.03 ~ 2024.06`

- 960만 건 환경에서 샤드 수 × 접근 방식 × 인덱스 전략 전 조합 측정 — Covering Index 14배 vs 샤딩 1.5배, **"샤딩 전에 인덱스부터"** 의사결정 기준 수립.

---

## 기술 스택

| 분류 | 주력 | 관심 / 도입 중 |
| --- | --- | --- |
| **언어** | Java, C#, Python | |
| **백엔드** | Spring Boot, FastAPI | OAuth2/OIDC (Zitadel IdP) |
| **데이터 & 메시징** | PostgreSQL (PostGIS), MySQL, Apache Kafka, SeaweedFS, Redis | |
| **포맷 & 프로토콜** | COG/BigTIFF, 3D Tiles (B3DM·glTF 2.0), 바이너리 직접 파싱 | CCSDS |
| **인프라 & DevOps** | AWS, Docker, GitHub Actions, Grafana, k6 | Kubernetes |

---

## 블로그 — [밑바닥부터](https://virtualworld.tistory.com/)

- **[분산 시스템]** [5,000개 파일을 원자적으로 배포하기 — Atomic Rename 패턴과 SeaweedFS](https://virtualworld.tistory.com/45)
- **[I/O 최적화]** [대용량 파일 멀티파트 업로드, 정말 빠를까?](https://virtualworld.tistory.com/62)
- **[운영체제]** [Zero-Copy — 파일 전송 최적화와 Kafka 코드 분석](https://virtualworld.tistory.com/20)
- **[백엔드]** [Spring 비동기 처리 — MVC 비동기 → WebFlux → Virtual Threads](https://virtualworld.tistory.com/54)
- **[분산 시스템]** [Dual Write와 데이터 정합성 — 전파 지연과 장애 복구 전략](https://virtualworld.tistory.com/53)
- **[네트워크]** [신뢰성을 위한 Timeout 처리와 멱등성 설계 전략](https://virtualworld.tistory.com/44)
- **[데이터베이스]** [PostgreSQL 내부 구조 시리즈 (0~6편)](https://virtualworld.tistory.com/63)

---

## 연락처

- 이메일: <liging12@naver.com>
- 링크드인: [linkedin.com/in/minseojo](https://www.linkedin.com/in/minseojo/)
- 블로그: [virtualworld.tistory.com](https://virtualworld.tistory.com/)
