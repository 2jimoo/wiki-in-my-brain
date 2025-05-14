# 엘라스틱서치 개요
- 자바 바이너리
- 키바나 클라이언트 사용
- Restful API 지원
- 메트릭과 버킷으로 데이터 집계
- JSON 도큐먼트 저장 후 인덱싱
  - 역인덱스, BKD 트리
- 검색
  - 관련성 점수(score), 매칭 건수(hits) 
 
# 기본 조작
- PUT
  - 문서 생성, _bulk
```
PUT /my-index/_doc/1
{
  "name": "Alice",
  "age": 30,
  "job": "developer",
  "created_at": "2024-05-14"
}

POST /my-index/_bulk
{ "index": { "_id": "2" } }
{ "name": "Bob", "age": 25, "job": "designer" }
{ "index": { "_id": "3" } }
{ "name": "Charlie", "age": 35, "job": "manager" }

```
- GET
  - _count, GET <index>/_doc/<id>, _search, _bulk
```
GET /my-index/_doc/1

GET /my-index/_count
{
  "query": {
    "match_all": {}
  }
}
```

# 검색 조건
- match_all
  - WHERE (... AND ...) 모든 조건이 만족된 것만
```
GET /my-index/_search
{
  "query": {
    "match_all": {}
  }
}
```
- match
  - WHERE ...
  - 암묵적으로 OR로 연결되므로 operator: AND 명시필요
```
GET /my-index/_search
{
  "query": {
    "match": {
      "job": {
        "query": "developer engineer",
        "operator": "and"
      }
    }
  }
}
```
- multi_match
  - WHERE (A=1 OR B=1 OR C=1 ...)
  - 여러개 필드들에 대해 값 검색
  - 부스팅(특정 필트에 우선순위 더 주는 것)계수 지정 가능
```
GET /my-index/_search
{
  "query": {
    "multi_match": {
      "query": "Alice",
      "fields": ["name", "job^2"]  // job 필드에 가중치 2배
    }
  }
}
```
- match_prase
  - 질의 모든 단어 질의 순서대로 포함하는 것만
  - slop개 만큼 누락 허용가능
  - fuzziness 설정으로 철자 오류처리(ex. 레벤슈타인 편집거리 알고리즘)
    - 삽입, 삭제, 교체 세 가지 연산으로 필요한 최소 연산 수
```
GET /my-index/_search
{
  "query": {
    "match_phrase": {
      "job": {
        "query": "software developer",
        "slop": 1
      }
    }
  }
}
GET /my-index/_search
{
  "query": {
    "match": {
      "name": {
        "query": "Alce",
        "fuzziness": "AUTO"
      }
    }
  }
}
```

# 텀 수준 쿼리
- 구조화된 쿼리
  - ex) 숫자, 날짜, 범위, IP주소 등 어떤 **패턴** 있으면 구조화되었다고 함
  - 구조화되지 않은 데이터를 풀텍스트라고 함
  - 풀텍스트는 분석(토큰화, 정규화)되어 저장되는데 텀 필드는 가공없이 그대로 저장
  - term(exact match), range 등
```
GET /my-index/_search
{
  "query": {
    "term": {
      "age": 30
    }
  }
}
GET /my-index/_search
{
  "query": {
    "range": {
      "age": {
        "gte": 25,
        "lte": 35
      }
    }
  }
}
```
- 복합쿼리 compound
  - bool, 상수 점수, 함수 점수, 부스팅, 디스정션 맥스 등을 조합해서 복잡한 조건 생성
  - bool
    -  must: match계열들이 일치해야함
    -  must_not: match계열들을 제외해야함
    -  should: 일치하면 관련성 점수 가산점(OR)
    -  filter: 결과 필터링만해서 보여줌, 점수에 영향X
```
GET /my-index/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "job": "developer" } }
      ],
      "must_not": [
        { "match": { "name": "Bob" } }
      ],
      "should": [
        { "match": { "name": "Alice" } }
      ],
      "filter": [
        { "range": { "age": { "gte": 25 } } }
      ]
    }
  }
}
```
   
# 집계
- 메트릭 집계
  - 숫자 연산 단순 집계
```
GET /my-index/_search
{
  "size": 0,
  "aggs": {
    "avg_age": {
      "avg": {
        "field": "age"
      }
    }
  }
}
```
- 버킷 집계
  - 날짜, 인구와 같은 그룹핑
  - 히스토그램, 시각화 등에 사용
```
GET /my-index/_search
{
  "size": 0,
  "aggs": {
    "age_ranges": {
      "histogram": {
        "field": "age",
        "interval": 5
      }
    }
  }
}
```
- 파이프라인 집계
  - 집계간 입출력 연결 
```
GET /my-index/_search
{
  "size": 0,
  "aggs": {
    "by_date": {
      "date_histogram": {
        "field": "created_at",
        "calendar_interval": "month"
      },
      "aggs": {
        "avg_age": {
          "avg": {
            "field": "age"
          }
        }
      }
    }
  }
}
```
