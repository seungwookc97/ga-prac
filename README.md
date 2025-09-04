```mermaid
graph TD
      subgraph "1️⃣ 키워드 생성 단계"
          A[서비스 시작] --> B[KeywordGenerationService]
          B --> B1[AI 키워드 생성]
          B1 --> B2[중복/반복 키워드 필터링]
          B2 --> B3[정적 키워드 추가]
          B3 --> B4[KeywordHistoryService<br/>사용 키워드 기록]
      end

      subgraph "2️⃣ 뉴스 수집 단계"
          C[NaverNewsService] --> C1[네이버 뉴스 API 호출]
          C1 --> C2[NaverNewsDto 생성]
          C2 --> D[NewsCrawlingService]
          D --> D1[뉴스 링크 크롤링]
          D1 --> D2[본문/이미지/기자 정보 추출]
          D2 --> D3[RealNewsDto 생성]
      end

      subgraph "3️⃣ 뉴스 분석 단계"
          E[NewsAnalysisService] --> E1[뉴스 품질 점수 계산]
          E1 --> E2[NewsDeduplicationService<br/>중복 뉴스 제거]
          E2 --> E3[AnalyzedNewsDto 생성]
          E3 --> F[NewsDataService<br/>점수 기반 선별]
          F --> F1[카테고리별 균등 분배]
      end

      subgraph "4️⃣ 실제 뉴스 저장"
          G[RealNews Entity 저장] --> G1[🔔 RealNewsCreatedEvent]
          G1 --> G2[뉴스 저장 완료]
      end

      subgraph "5️⃣ AI 콘텐츠 생성"
          H[FakeNewsService] --> H1[🤖 AI 가짜뉴스 생성]
          H1 --> H2[FakeNews Entity 저장]
          H2 --> H3[🔔 FakeNewsCreatedEvent]

          I[DetailQuizService] --> I1[🤖 AI 상세퀴즈 생성<br/>3개 객관식]
          I1 --> I2[DetailQuiz Entity 저장]

          J[FactQuizService] --> J1[진짜/가짜 구분 퀴즈 생성]
          J1 --> J2[FactQuiz Entity 저장]
      end

      subgraph "6️⃣ 오늘의 뉴스"
          K[👨‍💼 관리자 수동 선택] --> K1[TodayNewsService]
          K1 --> K2[TodayNews Entity 저장]
          K2 --> K3[🔔 TodayNewsCreatedEvent]

          L[DailyQuizService] --> L1[오늘의 뉴스 DetailQuiz 활용]
          L1 --> L2[DailyQuiz Entity 저장]
      end

      subgraph "7️⃣ 관리 및 정리"
          M[KeywordCleanupService] --> M1[🗑️ 오래된 키워드 삭제]
          N[KeepAliveMonitoringService] --> N1[💓 서비스 상태 체크]
      end

      %% 플로우 연결
      B4 --> C
      D3 --> E
      F1 --> G
      G2 --> H
      G2 --> I
      H3 --> J
      G2 --> K
      K3 --> L

      %% 정기 작업
      B4 -.-> M
      G2 -.-> N

      %% 스타일링
      classDef service fill:#e3f2fd,stroke:#1976d2,stroke-width:2px
      classDef ai fill:#e8f5e8,stroke:#388e3c,stroke-width:2px
      classDef event fill:#fff3e0,stroke:#f57c00,stroke-width:2px
      classDef entity fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
      classDef admin fill:#ffebee,stroke:#c62828,stroke-width:2px
      classDef cleanup fill:#fafafa,stroke:#424242,stroke-width:2px

      class B,C,D,E,F,I,J,K1,L service
      class B1,H1,I1 ai
      class G1,H3,K3 event
      class G,H2,I2,J2,K2,L2 entity
      class K admin
      class M1,N1 cleanup
