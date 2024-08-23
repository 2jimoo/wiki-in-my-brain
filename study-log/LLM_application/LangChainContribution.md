# Overview
### 챗봇 빌더 서비스에서 사용한 Langchain에서 효율적인 RAG를 지원하는 index api를 사용하고자 했을 때 부족한 기능을 구현 
- OpenSearch와 MongoDB를 사용하고 있었는데 index api를 완전히 지원하는  OpenSearch 기반 VectorStore 구현체, MongoDB 기반 RecordManager 구현체가 없었습니다. 
- OpenSearchVectorSearch에 삭제 기능과 비동기 함수들을 구현하고 MongoDB기반 NoSql document manager을 제안 및 구현하여 컨트리뷰션 하였습니다. 
- MongoDB document manager를 통해 문서가 재임베딩 및 중복 저장되는 것을 방지하기 위해 메타데이터를 관리하는 로직을 수행하고 OpenSearchVectorSearch 비동기 함수를 활용도록 했습니다.

# Proposal
- https://drive.google.com/file/d/16MziKro3NCao6WTn6IWuXExXy66x4Lsw/view?usp=drive_link 

# PR
- OpenSearch VectorStore Contribution(2024.04)
  - https://github.com/langchain-ai/langchain/pull/17321
- MongoDB Document Manager Contribution(2024.02)
  - https://github.com/langchain-ai/langchain/pull/17320
