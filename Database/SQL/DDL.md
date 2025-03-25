테이블 추가
```sql
CREATE TABLE [테이블 명] (
-- member_id NUMBER(10,0) NOT NULL PRIMARY KEY // 기본키 생성
-- member_id number(10,0) NOT NULL  REFERENCES member(member_id) // 외래키 생성
-- status varchar2(20)  DEFAULT 'N' NOT NULL, default value 
-- <컬럼명> <자료형(크기> [옵션] [제약조건],
-- [<컬럼명> <자료형(크기> [옵션] [제약조건], ...],
);
```

```sql
create sequence 시퀀스이름A;
```