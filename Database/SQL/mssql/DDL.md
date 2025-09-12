# MSSQL DDL(Data Definition Language)

## 테이블 생성
```sql
CREATE TABLE issued_coupon (
    id BIGINT NOT NULL AUTO_INCREMENT COMMENT '유저 아이디',
    name VARCHAR(100) NOT NULL COMMENT '유저 이름',
    team_id BIGINT  NOT NULL COMMENT '팀 ID',
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '생성 시간 (발급일)',
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '수정 시간',
    PRIMARY KEY (id),
    INDEX idx_user_id (user_id),
    CONSTRAINT fk_team_id FOREIGN KEY (user_id) REFERENCES user (id),
) COMMENT='유저 테이블';

```

## 컬럼 수정
```sql
ALTER TABLE [테이블명] ALTER COLUMN [컬럼명] [데이터타입]

-- ex
ALTER TABLE dbo.PAS_DIST_MNG ALTER COLUMN WEB_URL NVARCHAR(1000)
```















