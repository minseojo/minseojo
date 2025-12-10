### 👋 Introduction
백엔드 개발자 **조민서**입니다.  
**일관성·가용성·장애 복구·확장성**을 기준으로, 대규모 데이터를 안정적으로 처리하는 분산 시스템을 설계·구현하는 데 관심 있습니다.

현재 ICTWAY에서 3D Tiles 기반 3D GIS 엔진과 타일 빌드 파이프라인을 설계·구현하고 있습니다.  
대규모 공간 데이터를 분산 환경에서 안정적으로 저장·배포할 수 있는 백엔드 인프라를 만드는 일을 맡고 있습니다.

- 💼 ICTWAY | 3D GIS 솔루션 개발자 (2024.12 ~ 재직중)  
- 🎓 전북대학교 IT정보공학과 B.S. (2019.03 ~ 2025.02)  
- 🚀 소프트웨어 마에스트로 15기 연수생 (2024.04 ~ 2024.12)  

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

### 🧩 Projects

#### 1. 빌드 분산 파이프라인 및 저장 구조 개선
- **요약**: 단일 서버·로컬 DB에 묶여 있던 **모놀리식 빌드 시스템을**, 여러 워커 노드가 공용 메타데이터·스토리지를 공유하는 **MSA 기반 분산 빌드 아키텍처**로 전환했습니다.  
- **성과**:
     - 워커 노드 수에 비례해 **빌드 처리량 수평 확장 가능**
     - 병렬 파이프라인 설계로 **대규모 빌드 소요 시간을 수~수십 배 단축 가능한 구조 확보**
     - 빌드 산출물을 **공용 오브젝트 스토리지(AWS S3, SeaweedFS, S3 호환 스토리지 등)** 에 저장해, 서버 증설 시에도 애플리케이션 구조 변경 없이 확장 가능

#### 2. 공간 데이터 버전관리 시스템 설계 및 구현
- **요약**: 타일 단위로 빌드 결과를 식별·재사용하고, **변경된 영역만 다시 빌드하는 구조**를 설계했습니다.  
- **성과**:
     - 동일 타일은 기존 결과를 재사용하고, 변경된 타일만 재생성하는 **Selective Rebuild** 가능  
       (기존: 전체 재빌드 **수백 시간(Hour)** → 변경: 변경 구간만 **수 분(Minute)** 재빌드)
     - 전체 재빌드 없이, 변경된 공간만 **증분 빌드·버전 간 비교**가 가능한 구조 확보
     - 중복 빌드·중복 저장을 줄여 **스토리지·운영 비용 수십 % 절감**<br/>

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

### 📫 Contact
- **Email**: [liging12@naver.com](mailto:liging12@naver.com)  
- **LinkedIn**: [linkedin.com/in/minseojo](https://www.linkedin.com/in/minseojo/)  
- **Blog**: [virtualworld.tistory.com](https://virtualworld.tistory.com/)  

---

### 📊 GitHub Stats
<div align="center">
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=minseojo&langs_count=6&hide=html,css,tex&layout=compact" width="35%" />
  <img src="https://github-readme-stats.vercel.app/api?username=minseojo&show_icons=true&theme=vue" width="50%" />
</div>
