---
id: normalizing-state-shape
title: 상태 정규화하기
description: '리듀서 구조 잡기 > 상태 정규화하기: Why and how to store data items for lookup based on ID'
---

# 상태 정규화하기

실제로 많은 애플리케이션들은 관계가 있거나 중첩된 데이터를 처리합니다. 예를 들어 블로그 에디터는 많은 포스트를 가질 수 있고, 각각의 포스트는 많은 댓글이 있을 수 있으며, 포스트와 댓글은 사용자에 의해 작성됩니다. 이런 종류의 애플리케이션의 데이터는 아래와 같을 겁니다.

```js
const blogPosts = [
  {
    id: 'post1',
    author: { username: 'user1', name: 'User 1' },
    body: '......',
    comments: [
      {
        id: 'comment1',
        author: { username: 'user2', name: 'User 2' },
        comment: '.....'
      },
      {
        id: 'comment2',
        author: { username: 'user3', name: 'User 3' },
        comment: '.....'
      }
    ]
  },
  {
    id: 'post2',
    author: { username: 'user2', name: 'User 2' },
    body: '......',
    comments: [
      {
        id: 'comment3',
        author: { username: 'user3', name: 'User 3' },
        comment: '.....'
      },
      {
        id: 'comment4',
        author: { username: 'user1', name: 'User 1' },
        comment: '.....'
      },
      {
        id: 'comment5',
        author: { username: 'user3', name: 'User 3' },
        comment: '.....'
      }
    ]
  }
  // 많이 반복
]
```

데이터의 구조가 꽤 복잡하고 몇몇 데이터는 반복됩니다. 이는 몇 가지 이유에서 우려됩니다.

- 데이터의 조각이 여러 군데로 복사될 때 제대로 업데이트되었는지 확인하기 어렵게합니다.
- 데이터의 중첩은 리듀서의 로직 또한 중첩되고 복잡해짐을 의미합니다. 특히 깊은 곳에 있는 데이터를 업데이트하는 것은 매우 지저분하게 만들 수 있습니다.
- 불변데이터의 업데이트는 모든 상위 데이터들이 갱신되어야 하고 새로운 객체 참조는 연결된 UI 컴포넌트를 다시 렌더링하기 때문에, 깊이 중첩된 데이터를 업데이트 하는것은 관계없는 UI컴포넌트까지 데이터 변화가 없음에도 불구하고 강제로 다시 렌더링합니다.

이러한 이유로, 리덕스 저장소에서 중첩되거나 연관된 데이터를 처리할 때는 저장소를 마치 데이터베이스의 일부인 것처럼 정규화된 형태로 유지하는 방법으로 접근하시기를 권장합니다.

## 정규화된 상태의 설계

데이터 정규화의 기본 개념:

- 상태에서 각 데이터의 타입은 자신의 "테이블"을 가집니다.
- 각 "데이터 테이블"은 항목의 아이디를 키로, 항목들을 값으로 가지는 개별 항목 아이템을 저장해야 합니다.
- 개별 항목에 대한 참조는 항목의 ID를 저장하여 수행해야 합니다.
- 배열의 ID는 순서를 나타내야 합니다.

위의 블로그 예제에서 상태구조를 정규화하면 아래와 같을 겁니다.

```js
{
    posts : {
        byId : {
            "post1" : {
                id : "post1",
				author : "user1",
				body : "......",
				comments : ["comment1", "comment2"]
            },
            "post2" : {
				id : "post2",
				author : "user2",
				body : "......",
				comments : ["comment3", "comment4", "comment5"]
            }
        }
        allIds : ["post1", "post2"]
    },
    comments : {
        byId : {
            "comment1" : {
                id : "comment1",
                author : "user2",
                comment : ".....",
            },
            "comment2" : {
                id : "comment2",
                author : "user3",
                comment : ".....",
            },
            "comment3" : {
                id : "comment3",
                author : "user3",
                comment : ".....",
            },
            "comment4" : {
                id : "comment4",
                author : "user1",
                comment : ".....",
            },
            "comment5" : {
                id : "comment5",
                author : "user3",
                comment : ".....",
            },
        },
        allIds : ["comment1", "comment2", "comment3", "commment4", "comment5"]
    },
    users : {
        byId : {
            "user1" : {
                username : "user1",
                name : "User 1",
            }
            "user2" : {
                username : "user2",
                name : "User 2",
            }
            "user3" : {
                username : "user3",
                name : "User 3",
            }
        },
        allIds : ["user1", "user2", "user3"]
    }
}
```

이 상태 구조는 전체적으로 보다 평평합니다. 원래의 중첩된 형태에 비해 다음과 같은 몇 가지 측면에서 개선되었습니다.

- 각 항목이 하나의 위치만 정의하므로 아이템이 업데이트되더라도 여러 위치를 바꾸지 않아도 됩니다.
- 리듀서로직이 깊게 중첩된 데이터를 관리하지않아도 되기 때문에 더 간단합니다.
- 주어진 항목을 검색하거나 업데이트하는 로직이 매우 간단하고 일관성이 있습니다. 항목의 타입과 ID만 주어지면 다른 객체를 찾지 않고 몇 단계로 쉽게 찾을 수 있습니다.
- 데이터 타입이 분리되어 있기 때문에, 댓글의 텍스트를 변경하는 것과 같은 업데이트가 트리의 "comments > byId > comment" 부분의 새로운 복사본만을 필요로 합니다. 이는 보통 데이터변경으로 업데이트가 필요한 UI의 부분이 적다는 것을 의미합니다. 반면 원래의 중첩된 모양에서는 댓글 객체, 부모 포스트 객체, 모든 포스트 객체 배열의 업데이트가 필요하고 이는 아마 UI안의 _모든_ 포스트 컴포넌트와 댓글 컴포넌트를 다시 렌더링 할 겁니다.

상태 구조를 정규화하는 것은 보통 적은 연결의 컴포넌트가 많은 양의 데이터를 아래로 전달하는 것과 반대로 더 많은 컴포넌트가 연결되고 각 컴포넌트가 자체적으로 데이터를 조회해야 한다는 것을 의미합니다.

## 상태에서 정규화된 데이터 구성하기

전형적인 애플리케이션에서는 아마 관계있는 데이터와 그렇지 않은 데이터가 공존할 겁니다. 다른 데이터타입이 어떻게 구성되어야 하는지에 대한 규칙이 정확히 하나만 있는 것이 아니지만 이 중 하나는 관계있는 "테이블"을 "entities"와 같은 일반적인 부모 키 아래에 넣는 패턴입니다. 구조는 아래와 같을 겁니다.

```js
{
    simpleDomainData1: {....},
    simpleDomainData2: {....}
    entities : {
        entityType1 : {....},
        entityType2 : {....}
    }
    ui : {
        uiSection1 : {....},
        uiSection2 : {....}
    }
}
```

이는 여러 가지 방법으로 확장될 수 있습니다. 예를 들어 많은 수정이 일어나는 애플리케이션에는 두 가지 상태 "테이블"을 유지하고 싶을 겁니다. "현재"항목 값과 "진행단계" 항목 값입니다. 항목이 편집될 때, 이 값은 "진행단계"로 복사되고 이를 업데이트하는 액션은 "진행단계"에 복사될겁니다. 이는 UI의 다른 부분이 원래 버전을 참조하는 동안 해당 데이터로 편집 폼을 제어할 수 있습니다. 편집 폼을 "재지정"하는 것은 그저 "진행단계"의 항목을 지우고 "현재"섹션의 이전 데이터를 "진행단계"로 다시 복사하기만 하면 됩니다. 편집을 "적용"하는 동안 "진행단계"섹션의 값을 "현재"섹션으로 복사해야합니다.

## 관계와 테이블

우리는 리덕스 저장소를 "데이터베이스"의 부분으로 다루고 있기 때문에, 데이터베이스 설계의 많은 이론들이 여기에 또한 적용될 수 있습니다. 예를 들어 다대다(many-to-many) 관계를 가질 수 있고, 항목에 해당하는 아이디를 중간 테이블에 저장해서 모델링 할 수 있습니다. ("table join" 이나 "associative table") 이처럼 우리는 실제 테이블에서 사용한 것과 같이 'byId'혹은 'allIds'를 사용합니다:

```js
{
    entities: {
        authors : { byId : {}, allIds : [] },
        books : { byId : {}, allIds : [] },
        authorBook : {
            byId : {
                1 : {
                    id : 1,
                    authorId : 5,
                    bookId : 22
                },
                2 : {
                    id : 2,
                    authorId : 5,
                    bookId : 15,
                }
                3 : {
                    id : 3,
                    authorId : 42,
                    bookId : 12
                }
            },
            allIds : [1, 2, 3]

        }
    }
}
```

"특정 작가의 모든 책 조회"와 같은 작업은 조인 테이블에 대해 단일 루프로 처리할 수 있습니다. 클라이언트 애플리케이션의 일반적인 데이터양과 자바스크립트 엔진의 속도를 고려했을 때, 이런 종류의 작업은 대부분의 케이스에 대해 충분히 빠른 성능을 가질겁니다.

## 중첩된 데이터 정규화

API는 빈번히 데이터를 중첩해서 돌려보내기 때문에 데이터는 상태 트리에 넣기 전에 정규화된 모양으로 바꿔야 합니다. [Normalizr](https://github.com/paularmstrong/normalizr)는 이 작업을 위해 주로 사용되는 라이브러리입니다. 스키마 타입과 관계를 정의할 수 있습니다. 스키마와 데이터 응답을 Normalizr에게 주면 정규화된 응답을 출력합니다. 이 출력은 액션에 포함될 수 있으며 저장소를 업데이트합니다. 자세한 내용은 Normalizr 도큐먼트를 참조하세요.
