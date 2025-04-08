# Oracle DDL(Data Definition Language)

## 테이블 추가

```sql
CREATE TABLE [테이블 명] (
-- member_id NUMBER(10,0) NOT NULL PRIMARY KEY // 기본키 생성
-- member_id number(10,0) NOT NULL  REFERENCES member(member_id) // 외래키 생성
-- status varchar2(20)  DEFAULT 'N' NOT NULL, default value
-- <컬럼명> <자료형(크기> [옵션] [제약조건],
-- [<컬럼명> <자료형(크기> [옵션] [제약조건], ...],
);
```

## sequence 추가

```sql
create sequence [시퀀스이름];
```

## 컬럼 추가

```sql
ALTER TABLE WOR_CHART ADD AGGREGATION_PERIOD varchar(10) default "M" NOT NULL;
COMMENT ON COLUMN WOR_CHART.AGGREGATION_PERIOD IS '보고집계기간';
```

## 컬럼 삭제

```sql
ALTER TABLE tblqnaanswer DROP COLUMN qasubject;
```

## 컬럼 데이터 타입 변경

```sql
ALTER TABLE [테이블명] MODIFY [컬럼명] [변경할 데이터 타입];
```
