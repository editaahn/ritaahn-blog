---
title: "Apollo Client : useMutation의 options"
date: "2022-04-03T11:43:00"
categories: [apollo]
comments: true
---

useMutation API가 어떤 옵션을 받는지 자세히 살펴보자.

## Options

#### - 오퍼레이션 관련

| 이름/타입 | 설명 |
| --- | --- |
| mutation<br>`DocumentNode` | gql 템플릿 리터럴을 통해 AST로 파싱된 GraphQL query string<br><br>- useMutation 훅에는 선택 필드. mutation은 훅의 첫 번째 인자에 꼭 들어가는 값이므로.<br>- Mutation 컴포넌트를 사용 시에는 필수 필드. |
| variables<br>`{ [key: string]: any }` | mutation 실행을 위해 모든 GraphQL 변수들을 넣는 object<br>- key는 변수 이름, value는 변수 값을 넣는다. |
| errorPolicy<br>`ErrorPolicy` | GraphQL error와 일부 result를 반환하는 응답을 어떻게 처리할 건지 정의<br><br>- default value는 `none`<br>- `none`: GraphQL error가 발생하면, error.GraphQLErrors를 리턴하고, 서버가 data를 반환하더라도 응답된 data는 undefined이다. network 에러와 GraphQL 에러를 동일한 응답 형태로 취급한다는 것을 의미한다.<br>- `ignore`: graphQLErrors를 무시하고 캐시된 data를 반환한다. 에러가 없는 것처럼 렌더한다.<br>- `all`: data와 error.graphQLErrors가 모두 생성되고, 일부 results와 error 정보를 렌더할 수 있다. |
| onCompleted<br>`(data: TData | {}) => void` | mutation이 에러 없이 성공적으로 끝나면 호출되는 콜백 함수<br><br>- errorPolicy가 ignore라면 일부 데이터만 반환된다.<br>- 이 함수는 mutation의 result data를 전달받는다. |
| onError<br>`(error: ApolloError) => void` | mutation에 하나 이상의 에러가 발생하면 호출되는 콜백 함수<br>(errorPolicy가 ignore가 아닌 경우에)<br><br>- 이 함수는 ApolloError 객체를 전달 받는다. 발생한 에러에 따라 networkError object 또는 graphQLErrors 배열을 포함한다. |
| refetchQueries<br>`Array<string | { query: DocumentNode, variables?: TVariables}> | ((mutationResult: FetchResult) => Array<string | { query: DocumentNode, variables?: TVariables}>)` | mutation 발생 후 refetch하고 싶은 query들의 배열<br><Br>array의 각 값은 아래 두 유형이 가능하다<br>- query와 variables를 포함한 object<br>- refetch할 쿼리의 operation name (string) |
| onQueryUpdated<br>`(observableQuery: ObservableQuery, diff: Cache.DiffResult, lastDiff: Cache.DiffResult | undefined) => boolean | TResult` | mutation 후 캐시 데이터를 업데이트하는 쿼리들을 가로채는 콜백 함수(refetchQueries 필드 내에 포함되어 client.mutate로 전달된 쿼리들도 포함)<br><br>`onQueryUpdated가` Promise를 리턴하면, 이 Promise를 마지막 순서인 mutation Promise가 await한다. `false`를 리턴하면 쿼리는 무시된다. |
| awaitRefetchQueries<br>`boolean` | `true`면 refetchQueries의 모든 쿼리들이 모두 완료되고 나서 mutation이 완료된다.<br>- default value는 `false` (query들을 비동기로 refetch함) |
| ignoreResults<br>`boolean` | `true`면 mutation의 data 프로퍼티가 result로 업데이트되지 않는다.<br>- default value는 `false`  |

#### - Networking 관련

| 이름/타입 | 설명 |
| --- | --- |
| notifyOnNetworkStatusChange<br>`boolean` | true면, 네트워크 상태가 변경되거나 에러가 나타날 때 언제든, 진행중인 mutation의 컴포넌트가 리렌더된다.<br>- default value는 false |
| client<br>`ApolloClient` | ApolloClient의 인스턴스. mutation을 실행하는 데에 사용한다. 기본적으로 context를 통해서 내려온 인스턴스가 사용되나, 여기서 다른 인스턴스를 제공할 수 있다. |
| context<br>`Record<string, any>` | Apollo Link를 사용하고 있다면, 이 object는 link chain으로 전달되는 `context` object의 초기값이 된다. |

#### - 캐싱 관련

| 이름/타입 | 설명 |
| --- | --- |
| update<br>`(cache: ApolloCache, mutationResult: FetchResult) => void` | mutation 완료 후 Apollo Client 캐시를 업데이트할 떄 쓰는 함수<br>- [자세한 내용](https://www.apollographql.com/docs/react/data/mutations#updating-the-cache-after-a-mutation) |
| optimisticResponse<br>`Object` | 이 값이 제공되면, mutation이 완료되기 전에 임시 응답을 캐시한다. 즉각적인 UI 업데이트가 가능하다.<br>- [자세한 내용](https://www.apollographql.com/docs/react/performance/optimistic-ui/) |
| fetchPolicy<br>`MutationFetchPolicy` | - mutation의 결과로 아폴로 캐시를 업데이트하지 않으려면 `no-cache`를 옵션으로 제공하면 된다. <br>- 기본값은 `network-only`이다.<br>- query와는 다르게, mutation은 위 2개 말고는 fetchPolicy를 제공하지 않는다. |