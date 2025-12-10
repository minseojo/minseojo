### 👋 Introduction
백엔드 엔지니어 **조민서**입니다.  
**일관성·가용성·장애 복구·확장성**을 기준으로, 대규모 데이터를 안정적으로 처리하는 분산 시스템을 설계·구현하는 데 관심 있습니다.

현재 ICTWAY에서 3D Tiles 기반 3D GIS 엔진과 타일 빌드 파이프라인을 설계·구현하고 있습니다.  
대규모 공간 데이터를 분산 환경에서 안정적으로 저장·배포할 수 있는 백엔드 인프라를 만드는 일을 맡고 있습니다.

- 💼 ICTWAY | 3D GIS 엔진 & 백엔드 개발자 (2024.12 ~ 재직중)  
- 🎓 전북대학교 IT정보공학과 B.S. (2019.03 ~ 2025.02)  
- 🚀 소프트웨어 마에스트로 15기 연수생 (2024.04 ~ 2024.12)  

---

### 🧩 Projects

#### 1. 저장 구조 개선 & 모놀리식 시스템 → 마이크로서비스 아키텍처 전환
- **요약**: 단일 서버·로컬 DB에 흩어져 있던 메타데이터를 중앙 DB(PostgreSQL)와 오브젝트 스토리지로 통합하고,  
  Kafka 기반 이벤트로 여러 워커 노드가 공용 메타데이터를 읽어 빌드·배포하는 **마이크로서비스 아키텍처**로 모놀리식 빌드 시스템을 전환했습니다.
- **성과**:
     - 워커 노드 수에 비례해 **빌드 처리량 수평 확장 가능**
     - 병렬 파이프라인 설계로 **대규모 빌드 소요 시간을 수~수십 배 단축 가능한 구조 확보**
     - 빌드 산출물을 **공용 오브젝트 스토리지(AWS S3, SeaweedFS, S3 호환 스토리지 등)** 에 저장해, 서버 증설 시에도 애플리케이션 구조 변경 없이 확장 가능

#### 2. 공간 데이터 버전관리 시스템 설계 및 구현
- **요약**: 타일을 Hash로 중복 식별 및 재사용하고, **변경된 영역만 다시 빌드하는 구조**를 설계 및 구현했습니다.  
- **성과**:
     - 동일 타일은 기존 결과를 재사용하고, 변경된 타일만 재생성하는 **Selective Rebuild** 가능  
       (기존: 전체 재빌드 **수백 시간(Hour)** → 변경: 변경 구간만 **수 분(Minute)** 재빌드)
     - 전체 재빌드 없이, 변경된 공간만 **증분 빌드·버전 간 비교**가 가능한 구조 확보
     - 중복 빌드·중복 저장을 줄여 **빌드 시간과 스토리지·운영 비용 수십 % 절감**<br/>

#### 3. 3D GIS 엔진 자료구조 전환 (3D Tiles 1.0 → 1.1) 
- **요약**: Cesium 오픈소스를 참고해 3D 타일 탐색 자료구조를 재구현하고, **타일별 랜덤 액세스가 가능한 구조**로 전환했습니다.  
- **성과**:
     - 3D 모델 렌더링 FPS **약 20% 향상**
     - 메타데이터(JSON) 용량 **약 30~40% 절감**
     - 타일 탐색 비용을 **O(log N) → O(1)** 로 단축

---
### 🔥 Interests

#### 1. 실시간 대규모 데이터 스트리밍 & 분산 시스템
   - Kafka 기반 이벤트 주도 아키텍처(EDA) 설계  
   - 고처리량·저지연 데이터 파이프라인 설계 및 운영  
   - 장애 허용·수평 확장 가능한 분산 시스템 구조 설계  
   - Kafka / Redis 내부 구조, 파일 시스템, 오브젝트 스토리지, 대용량 파일 업로드 처리  

#### 2. MSA 환경에서의 안정적인 트랜잭션 처리 & 네트워크 장애 대응
   - 분산 환경에서의 트랜잭션 경계 정의, SAGA / Outbox 패턴 등을 통한 **데이터 일관성 확보**  
   - 서비스 간 호출에서 **네트워크 타임아웃, 재시도 정책, Circuit Breaker** 등을 활용한 장애 격리  
   - 실패를 전제로 한 설계: **Idempotent 처리, 재처리 가능 구조, 부분 실패에 대한 롤백/보상 설계**

---

### 🛠 Tech Stack
- **Languages**: Java, C#, Python, JavaScript  
- **Backend**: Spring Boot(MVC), JPA/Hibernate
- **Database**: PostgreSQL, MySQL, Redis, MongoDB, SQLite
- **Storage**: Amazon S3, SeaweedFS
- **Messaging**: Apache Kafka, AWS SQS  
- **Cloud**: AWS (EC2, RDS, S3, CloudFront, Lambda)  
- **Frontend**: React, React Native  
- **DevOps**: Docker, GitHub Actions
- **Monitoring**: Grafana, k6

---
### 📚 Blog Posts

> 더 많은 글은 블로그에서 볼 수 있습니다 👉 [virtualworld.tistory.com](https://virtualworld.tistory.com)

- [신뢰성을 위한 Timeout 처리와 멱등성 설계 전략](https://virtualworld.tistory.com/44)  
  의도치 않은 네트워크 예외 상황을 미리 가정하고, **Timeout·재시도·멱등성**으로 시스템 일관성을 지키는 방법을 정리한 글입니다.

- [[Log-based System] RDBMS와 Kafka 구조 분석](https://virtualworld.tistory.com/19)  
  RDBMS와 Kafka가 `로그(Log)`를 중심으로 어떻게 데이터의 일관성과 내구성을 보장하는지 비교·정리한 글입니다.

- [CAP 이론과 PACELC 트레이드오프 정리](https://virtualworld.tistory.com/29)  
  분산 시스템에서 **일관성·가용성·지연** 사이의 트레이드오프를 정리한 글입니다.

- [[Zero-Copy] 파일 전송 최적화와 Kafka 코드 분석](https://virtualworld.tistory.com/20)  
  `sendfile` 기반 Zero-Copy가 어떻게 커널 버퍼 복사를 줄이고, **고성능 파일/메시지 전송**을 가능하게 하는지 실험과 함께 설명한 글입니다.

- [대규모 공간 데이터 처리와 Implicit Tiling 설계](https://virtualworld.tistory.com/23)  
  프로젝트 **3D GIS 엔진 자료구조 전환 (3D Tiles 1.0 → 1.1)** 항목과 연결되는, 실제 설계·구현 과정을 정리한 글입니다.

- [[Z-order Indexing] 공간 데이터 최적화](https://virtualworld.tistory.com/21)  
  Morton Code(Z-order)를 활용해 대규모 공간 데이터를 **캐시 친화적이고 정렬된 인덱스**로 관리하는 방법을 정리한 글입니다.


---

### 📫 Contact
- **Email**: [liging12@naver.com](mailto:liging12@naver.com)  
- **LinkedIn**: [linkedin.com/in/minseojo](https://www.linkedin.com/in/minseojo/)  
- **Blog**: [virtualworld.tistory.com](https://virtualworld.tistory.com/)  


