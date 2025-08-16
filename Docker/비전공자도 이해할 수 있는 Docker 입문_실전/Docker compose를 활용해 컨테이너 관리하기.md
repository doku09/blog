## Docker Compose를 사용하는 이유
### ✅ Docker Copmose란?
여러 개의 Docker 컨테이너들을 하나의 서비스로 정의하고 구성해 하나의 묶음으로 관리할 수 있게 도와주는 툴이다.
### ✅ Docker Compose를 사용하는 이유
1. **여러 개의 컨테이너를 관리하는 데 용이**  
   여러 개의 컨테이너로 이루어진 복잡한 애플리케이션을 한 번에 관리할 수 있게 해준다. 여러 컨테이너를 하나의 환경에서 실행하고 관리
   하는 데 도움이 된다.
2. **복잡한 명령어로 실행시키던 걸 간소화 시킬 수 있음**  
   이전에 MySQL 이미지를 컨테이너로 실행시킬 때 아래와 같은 명령어를 실행시켰다.  
   ```
   $ docker run -e MYSQL_ROOT_PASSWORD=password123 -p 3306:3306 -v /Users/jaeseong/Documents/Develop/doc
   ```
너무 복잡하지 않은가? Docker Compose를 사용하면 위와 같이 컨테이너를 실행시킬 때마다 복잡한 명령어를 입력하지 않아도 된다. 단순히 docker compose up 명령어만 실행시키면 된다. 

### 예시 
compose 파일로 컨테이너를 시키는 방법을 살펴보면, 기존에는 cmd에서 명령어를 하나하나 입력해야 했다.  
`docker run --name webserver -d -p 80:80 nginx`
이 명령어를 compose 파일로 옮겨보면 다음과 같다.  
#️⃣ **compose.yml**
```
services: # 도커에서는 컨테이너를 service로 부름
  my-web-server: #서비스(컨테이너)의 이름
    container_name: web-server
    image: nginx
    ports:
      - 80:80
```

## 자주 사용하는 Docker Compose CLI 명령어

### 컴포즈 파일로 컨테이너 실행  
⌨️ command: `docker compose up`  
❗포그라운드에서 컨테이너 실행  
⌨️ command: `docker compose up -d`  
❗백그라운드에서 컨테이너 실행
> compose.yml 파일이 있는 경로에서 실행해야한다
### 도커파일의 이미지를 다시 빌드해서 컨테이너를 올려야할때
⌨️ command: `docker compose up -d --build`

### 컴포즈에 있는 이미지를 다운 받거나 업데이터해야할 때
⌨️ command: `docker compose pull`

### compose파일에 정의되어있는 컨테이너 중 실행되고있는 컨테이너 목록
⌨️ command: `docker compose ps`

### compose파일에 정의되어있는 컨테이너 중 모든상태의 컨테이너 목록
⌨️ command: `docker compose ps -a`

### 모든 컨테이너에서 발생한 모든 로고를 조회
⌨️ command: `docker compose logs`

### 컴포즈로 실행한 컨테이너 종료
⌨️ command: `docker compose down`

## [실습] DockerCompose로 MySql 실행시키기
```
# docker run -e MYSQL_ROOT_PASSWORD=pwd1234 -p 3306:3306 -v [호스트컴퓨터 경로]:[컨테이너 컴퓨터 경로] -d mysql
services:
  my-db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: pwd1234
    volumes:
      - ./mysql_data:/var/lib/mysql
    ports:
      - 3306:3306
```

## [실습] DockerCompose로 백엔드(springboot) 실행시키기
```
#docker build -t my-server . > compose파일과 Dockerfile이 같은 경로에 있기때문에 .를 붙인다.
services:
  my-server:
    build: .
    ports:
      - 8080:8080
```
compose파일이 Dockerfile을 찾아서 build하여 이미지로 만들고 run한다.  
❗이떄 Dockerfile의 내용이 변경되어 이미지가 변경될수 있으므로 다시 빌드하고 compose파일로 컨테이너를 실행한다.  
⌨️ command: `docker compose up -d --build`

