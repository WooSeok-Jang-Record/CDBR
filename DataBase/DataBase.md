**1. SQL과 NoSQL의 이해**

    -  **SQL (Structed Query Language)**
   
   **SQL은**<mark style='background-color: #dcffe4'> RDBMS(관계형 데이터 베이스 관리 시스템)의 데이터를 관리하기 위해 설계된 프로그래밍 언어 </mark>로, NoSQL보다
    오래되고 많이 사용되어 왔다. SQL의 예시로는 MySQL, PostgreSQL 등이 있다.
    
    - **NoSQL (Not Only Structed Query Language)**
   
   **NoSQL** 은 앞에 정리한 SQL보다 덜 제한적인 모델을 이용해 데이터의 저장 및 검색 메커니즘을 제공한다. NoSQL의 예시로는 mongoDB, redis 등이 있다.

**2. SQL, NoSQL 장단점**
  - **SQL의 장점**
    - 명확하게 정의된 스키마, 데이터 무결성 보장
    - 관계는 각 데이터를 중복없이 한번만 저장.
  
  - **NoSQL의 장점**
    - 스키마가 없기때문에, 훨씬 더 유연합니다. 즉, 언제든지 저장된 데이터를 조정하고 새로운 "필드"를 추가할 수 있습니다.
    - 데이터는 애플리케이션이 필요로 하는 형식으로 저장됩니다. 이렇게 하면 데이터를 읽어오는 속도가 빨라집니다.
    - 수직 및 수평확장이 가능하므로 데이터베이스가 애플리케이션에서 발생시키는 모든 읽기/쓰기 요청을 처리 할 수 있습니다.

  - **SQL의 단점**
    - 상대적으로 덜 유연합니다. 데이터 스키마는 사전에 계획되고 알려져야 합니다. (나중에 수정하기가 번거롭거나 불가능할수 도 있습니다.)
    - 관계를 맺고 있기 때문에, JOIN문이 많은 매우 복잡한 쿼리가 만들어 질 수 있습니다.
    - 수평적 확장이 어렵고, 대체로 수직적 확장만 가능합니다. 즉 어떤 시점에서 (처리할 수 있는 처리량과 관련하여) 성장 한계에 직면하게 됩니다.