# 타입스크립트의 Record

Record는 `Record<Key,Value>` 형식으로 사용하며 키가 key 타입이고 값이 value 타입인 객체 타입을 생성한다.

인덱스 시그니처와 유사한 기능을 한다.

```
type Score = {
	[name: string]: number;
}

// Score와 동일한 역할
type ScoreRecord = Record<string, number>;

let scores: ScoreRecord = {
  '차은우': 100,
	'장원영영': 200,
};
```

### 인덱스 시그니처와 Record 차이점

1. 리터럴 타입 사용 가능여부
   인덱스 시그니처는 리터럴 타입사용이 불가능하지만 Record는 가능하다.

```
type Foods = "hamburger" | "pizza";

type Menu = {
  [key: Foods]: number; // 에러: Key 리터럴 타입 사용이 불가능합니다.
};

const menu: Menu = {
  hamburger: 100,
  pizza: 200,
};

type MenuRecord = Record<Foods, number>;

const menuRecord: MenuRecord = {
  hamburger: 100,
  pizza: 200,
};
```

2. Key의 명시성

- 인덱스 시그니처는 키값의 의도를 명확히 할 수 있다.
- Record는 키값의 타입만 정의한다.

```
 type Menu = {
    [food:string]:number;
  }
  let food:Menu = {
    'apple': 1,
    'banana': 2,
    'cherry': 3
  };

  type MenuRecord = Record<string, number>;
```
