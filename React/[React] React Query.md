# React Query란? 

![Desktop - 9](https://github.com/chaedev3/algorithm/assets/109324466/11095233-fd1c-4881-a1da-3a581c7e90c5)

<br>

#### ❓ React-Query란?

> **_Fetching, Caching, 서버 데이터와의 동기화를 지원해주는 라이브러리_**
> 즉, 서버의 값을 클라이언트에 가져오거나, 캐싱, 값 업데이트, 에러핸들링 등 비동기 과정을 더욱 편하게 하는데 사용됨

<br>

## ⭐ React Query의 장점 


👌** 카카오페이 프론트엔드 개발자들이 React Query를 선택한 이유 3줄 요약 **
1. React Query는 React Application에서 서버 상태를 불러오고, 캐싱하며, 지속적으로 동기화하고 업데이트하는 작업을 도와주는 라이브러리입니다.
2. 복잡하고 장황한 코드가 필요한 다른 데이터 불러오기 방식과 달리 React Component 내부에서 간단하고 직관적으로 API를 사용할 수 있습니다.
3. 더 나아가 React Query에서 제공하는 캐싱, Window Focus Refetching 등 다양한 기능을 활용하여 API 요청과 관련된 번잡한 작업 없이 “핵심 로직”에 집중할 수 있습니다. 

<br>

#### 1) 캐싱(Caching)
cf. 캐싱 : 특정 데이터의 복사본을 저장하여 이후 동일한 데이터의 재접근 속도를 높이는 것 
> react-query 는 **캐싱**을 통해 데이터에 대한 **반복적인 비동기 데이터 호출을 방지**하고, 이는 불필요한 API 호출을 줄여 서버에 대한 부하를 줄이는 좋은 결과를 가져옴 

<br>


**그렇다면 언제 데이터를 갱신해야할까?** 
- react-query 에서는 **최신의 데이터**를 **fresh**한 데이터, **기존의 데이터**를 **stale**한 데이터라고 말함 
- react-query 에서는 기본적인 아래의 **옵션**을 제공함 
```javascript
refetchOnWindowFocus, //default: true
refetchOnMount, //default: true
refetchOnReconnect, //default: true
staleTime, //default: 0
cacheTime, //default: 5분 (60 * 5 * 1000)
```
1) **refetchOnWindowFocus**: 브라우저에 포커스가 들어온 경우 새로운 데이터 패칭
- false 로 설정할 경우 브라우저가 포커스 되어도 데이터를 가지고 오지 않음
```javascript
const { data, isLoading } = useQuery('super-heroes', fetchSuperHeroes, {
  refetchOnWindowFocus: true, // or false
});
```
2) **refetchOnMount** : 새로운 컴포넌트 마운트가 발생한 경우 새로운 데이터 패칭 
-  false 로 설정할 경우 마운트 시 새로운 데이터를 가지고 오지 않음
```javascript
const { data, isLoading } = useQuery('super-heroes', fetchSuperHeroes, {
  refetchOnMount: true, // or false
});
```
3) **refetchOnReconnect**: 네트워크 재연결이 발생한 경우 데이터 패칭 

4) **staleTime**: **staleTime은 데이터가 fresh -> stale한 상태로 변경되는 데 걸리는 시간**이며 fresh한 상태일 경우에는 위의 3가지 경우가 발생해도 refetch가 일어나지 않음 
- API 요청 횟수를 줄일 수 있음 
```javascript
// staleTime을 3초로 설정
const { data, isLoading } = useQuery('super-heroes', fetchSuperHeroes, {
  staleTime: 3000,
});
```
5) **cacheTime**: **cacheTime은 데이터가 inactive 한 상태일 때 캐싱된 상태로 남아있는 시간**으로 예를 들면 특정 컴포넌트가 unmount(페이지 전환 등으로 화면에서 사라질 때) 되면 데이터는 inactive 상태로 바뀌고, 이 때 데이터는 cacheTime 만큼 유지됨, cacheTime 이후에 데이터는 가비지 콜렉터로 수집되고 메모리에서 해제되며 cacheTime이 지나지 않았는데 다시 mount 된다면, 새로운 데이터를 fetch 해오는 동안 캐싱된 데이터를 임시로 보여주는 것임  
> 즉, unmount 된 후 어느 시점까지 메모리에 데이터를 저장하여 캐싱할 것인지를 결정
```javascript
// cacheTime을 3초로 설정
const { data, isLoading } = useQuery('super-heroes', fetchSuperHeroes, {
  cacheTime: 3000,
});
```

<br>

#### 2. Client 데이터와 Server 데이터 간의 분리 

프로젝트 규모가 커지고 관리해야 할 데이터가 많으면, Client에서 관리하는 데이터와 Server에서 관리하는 데이터가 분리될 필요성을 느낄 것이다. (Server 데이터는 비동기 API 호출을 통해 불러오는 데이터라고 생각하면 됨)

Client 데이터의 경우 Redux, Recoil, Zustand 등 전역 상태 관리 라이브러리를 통해 관리되어오고 있으나, 이러한 라이브러리들이 Server 데이터까지도 관리를 해야하는 상황이 발생한다는 것임. 물론 비동기 함수를 처리하는 로직이 존재하거나, 서드 파티 라이브러리를 지원하긴 하지만 Client 데이터를 관리하는데 로직이 집중되어 있기 때문에, Server 데이터까지 효율적으로 관리하기에는 한계가 분명함

> react-query는 이러한 문제를 해결해줌, 즉, **Client 데이터**는 **상태 관리 라이브러리**가 관리하고, **Server의 데이터**는 **react-query**가 관리함. 이를 통해 Client 데이터와 Server 데이터를 **온전하게 분리**할 수 있음


이 외에도 ... 
- 무한 스크롤 (Infinite Queries)
- get을 한 데이터에 대해 update를 하면 자동으로 get을 다시 수행한다. (예를 들면 게시판의 글을 가져왔을 때 게시판의 글을 생성하면 게시판 글을 get하는 api를 자동으로 실행 )
- 데이터가 오래 되었다고 판단되면 다시 get (invalidateQueries) 
- 비동기 과정을 선언적으로 관리할 수 있다. 

등의 장점이 존재함

<br>

## ⭐  react-query 기본  
기본적으로는 GET에는 **useQuery**, PUT, UPDATE, DELETE에는 **useMutation**이 사용됨 

<br>

### 0. 설치 및 초기 설정
```
npm i react-query
```

```
import { QueryClientProvider, QueryClient } from 'react-query';

const queryClient = new QueryClient();

function App() {
	return <QueryClientProvider client={queryClient}></QueryClientProvider>; 
}
export default App; 
```

<br>

### 1. 기존 요청 방식 vs react-query 요청 방식

- 기존 요청 방식: isLoading 과 data를 state로 가지며 서버 데이터를 불러온 후 상태 update 
- react-query : useQuery 훅을 이용해 반환받은 isLoading과 data 사용

#### 💗  기존 요청 방식 

```javascript
const Example = () => {
  const [isLoading, setIsLoading] = useState(true);
  const [data, setData] = useState([]);

  useEffect(() => {
    axios.get("http://localhost:8080/api").then((res) => {
      setData(res.data);
      setIsLoading(false);
    });
  }, []);

  if (isLoading) {
    return <h2>Loading...</h2>;
  }

  return (
    <>
      {data.map((hero) => {
        return <div>{hero.name}</div>;
      })}
    </>
  );
};

```
#### 💗  React Query 

```javascript
const fetchAPI = () => {
  return axios.get("http://localhost:8080/api");
};

const RQEaxmple = () => {
  const { data, isLoading } = useQuery("example", fetchAPI);

  if (isLoading) {
    return <h2>Loading...</h2>;
  }

  return (
    <>
      {data?.data.map((hero) => (
        <div key={hero.name}>{hero.name}</div>
      ))}
    </>
  );
};

```

<br>

### 2. useQuery 

- GET요청과 같은 CREATE작업을 할때 사용되는 훅

```
const responseData = useQuery(쿼리키, 쿼리함수, 옵션) 

const { data, isLoading, isFetching, isError, error } = useQuery('super-heros', fetchSuperHeroes)   
```

> 쿼리 키가 다르면 호출하는 API가 같더라도 캐싱을 별도로 관리한다는 점을 주의!! 
> useQuery 는 비동기로 작동! 즉, 한 컴포넌트에 여러개의 useQuery가 있다면 하나가 끝나고 다음 useQuery가 실행되는 것이 아닌 두개의 useQuery가 동시에 실행됨 (비동기로 여러개 실행할 경우 useQueries 사용) 
- data: 서버 요청에 대한 데이터 
- isLoading : 캐시가 없는 상태에서의 데이터 요청 중인 상태 (t/f) 
- isFetching : 캐시의 유무와 상관없이 데이터 요청 중인 상태 (t/f)
- isError: 서버 요청 실패에 대한 상태 (t/f) 
- error: 서버 요청 실패(object) 

- 옵션에는 위에서 봤던 refetchOnWindowFocus, refetchOnMount, refetchReconnect, staleTime, cacheTime 외에도 refetchInterval, refetchIntervalInBackground, enabled, onSuccess, onError 등이 있음 

- refetchInterval : 지정한 시간 간격만큼 데이터 페칭 (단, 브라우저에 포커스가 없을 때 실행되지 않음)
```javascript 
// 2초 간격으로 데이터 패칭
const { data, isLoading } = useQuery('super-heroes', fetchSuperHeroes, {
  refetchInterval: 2000,
});
```
- refetchIntervalInBackground : 지정한 시간 간격만큼 데이터 페칭 (브라우저에 포커스가 없어도 실행됨) 
```javascript
// 2초 간격으로 데이터 패칭
const { data, isLoading } = useQuery('super-heroes', fetchSuperHeroes, {
  refetchInterval: 2000,
  refetchIntervalInBackground: true,
});
```
- enabled: 컴포넌트가 마운트 되어도 데이터 패칭되지 않음! 
> 코드를 동기적으로 수행해야 하는 상황에 사용! refetch를 활용하여 데이터 패칭을 할 수 있음 
```javascript
 const { data, isLoading, refetch } = useQuery('super-heroes', fetchSuperHeroes, {
  enabled: false,
});

return (
  <button onClick={ refetch }>Fetch Button</button>
) 
```
- onSuccess: 데이터 패칭 성공
```javascript
const { data, isLoading } = useQuery('super-heroes', fetchSuperHeroes, {
  onSuccess: (data) => {
  	console.log('데이터 요청 성공', data)
  }
});
```
- onError: 데이터 패칭 실패 
```javascript
const { data, isLoading } = useQuery('super-heroes', fetchSuperHeroes, {
  onError: (error) => {
  	console.log('데이터 요청 실패', error)
  }
}); 
```

<br>

### 3. useMutation 

- POST, PUT, DELETE 와 같은 변경 및 수정작업을 할 때 사용되는 훅
```
const requestData = useMutation(API 호출 함수, 콜백); 
```
- API 호출 함수: 특정 endpoint로 요청을 보내고 Promise를 반환하는 함수 
- 콜백: 라이프사이클에 따라 로직 작성

<br>

#### 💗 예시

```javascript
import { useMutation, useQueryClient } from 'react-query';

const AddSuperHero = () => {
  ✅ const queryClient = useQueryClient();
  
  const addSuperHero = (hero) => {
    return axios.post('http://localhost:4000/superheroes', hero);
  };
  
  const { mutate: addHero, isLoading, isError, error } = useMutation(addSuperHero, {
    onSuccess: () => {
    // 캐시가 있는 모든 쿼리 무효화
    ✅ queryClient.invalidateQueries();
    
    // queryKey가 'super-heroes'로 시작하는 모든 쿼리 무효화
    ✅ queryClient.invalidateQueries('super-heroes');
    }
  });

  const handleAddHeroClick = () => {
    const hero = { 이름, 성별 };
    addHero(hero);
  };

  if (isLoading) {
    return <h2>Loading...</h2>;
  }

  if (isError) {
    return <h2>{error.message}</h2>;
  }
}
```

<br>

#### 💗 콜백함수 작성 예시

> mutate 함수가 실행되기 전, 성공 여부, 끝과 같이 라이프 사이클에 따라 콜백함수를 작성할 수 있음

```javascript
useMutation(addSuperHero, {
   onMutate: (variables) => {
     // mutate 함수가 실행되기 전에 실행
     console.log(variables) // addSuperHero에 들어가는 인자
   },
   onSuccess: (data, variables) => {
     // 성공
   },
   onError: (error, variables) => {
     // 에러 발생
   },
   onSettled: (data, error, variables, context) => {
     // 성공 or 실패 상관 없이 실행
   },
 })
```

<br>

#### **📒 참고** 

https://tanstack.com/query/v4/docs/react/overview 
https://velog.io/@kandy1002/React-Query-%ED%91%B9-%EC%B0%8D%EC%96%B4%EB%A8%B9%EA%B8%B0
https://kyounghwan01.github.io/blog/React/react-query/basic/#%E1%84%89%E1%85%A1%E1%84%8B%E1%85%AD%E1%86%BC%E1%84%92%E1%85%A1%E1%84%82%E1%85%B3%E1%86%AB-%E1%84%8B%E1%85%B5%E1%84%8B%E1%85%B2
https://velog.io/@jkl1545/React-Query 

