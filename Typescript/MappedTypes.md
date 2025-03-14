# 맵드 타입

객체의 속성들을 순회해서 속성의 타입을 다른 타입으로 바꿔주는 역할을 한다.

## Obj의 객체 속성의 타입들을 number로 바꿔준다.

```
interface Obj {
   prop1: string;
   prop2: string;
}

type ChangeType<T> = {
   [K in keyof T]: number;
};

type Result = ChangeType<Obj>;

```

## Person객체의 속성타입을 옵셔널 혹은 readonly로도 변경할 수 있다.

```
interface Person {
   name: string;
   age: number;
}

type ReadOnly<T> = {
   readonly [P in keyof T]: T[P];
};

type ParTial<T> = {
   [P in keyof T]?: T[P];
};

type PersonPartial = Partial<Person>;

type ReadonlyPerson = Readonly<Person>;
```

## 맵드 타입 활용 예제

```
type Prop = 'prop1' | 'prop2';

type Make<T> = {
	[K in Prop]: T
};

type T1 = Make<boolean>;
/*
type T1 = {
    prop1: boolean;
    prop2: boolean;
}
*/


type T2 = Make<number>;
/*
type T2 = {
    prop1: number;
    prop2: number;
}
*/


type T3 = Make<string>;
/*
type T3 = {
    prop1: string;
    prop2: string;
}
*/
```

# 고급타입 -Conditional Types

조건부 타입이라고도 불린다. 조건부타입이란 입력된 제네릭 타입에 따라 타입을 결정하는 기능을 말한다.
`T extends U ? X : Y`
위 코드는 인자로 받은 T가 U타입에 포함될 수 있으면 X타입으로 쓰고 아니면 Y타입으로 쓰라는 코드다

> extends는 상속같은 문법이 아니라 그냥 타입스크립트의 별개의 타입문법으로 취급하는게 좋다.

## 예제

```
// T extends U ? X : Y

// 제네릭이 string이면 문자열배열, 아니면 넘버배열
type IsStringType<T> = T extends string ? string[] : number[];

type T1 = IsStringType<string>; // type T1 = string[]
type T2 = IsStringType<number>; // type T2 = number[]

const a: T1 = ['홍길동', '임꺾정', '박혁거세'];
const b: T2 = [1000, 2000, 3000];
```

```
// 제네릭 `T`는 `boolean` 타입으로 제한.
// 제네릭 T에 true가 들어오면 string 타입으로, false가 들어오면 number 타입으로 data 속성을 타입 지정
interface isDataString<T extends boolean> {
   data: T extends true ? string : number;
   isString: T;
}

const str: isDataString<true> = {
   data: '홍길동', // String
   isString: true,
};

const num: isDataString<false> = {
   data: 9999, // Number
   isString: false,
};
```

---

> 출처: https://inpa.tistory.com/entry/TS-📘-타입스크립트-Mapped-types-완벽-이해하기 [Inpa Dev 👨‍💻:티스토리]
