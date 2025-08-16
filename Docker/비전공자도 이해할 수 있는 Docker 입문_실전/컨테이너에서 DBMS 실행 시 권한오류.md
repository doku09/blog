mysql 을 컨테이너로 실행시 실행되었다가 오류가나서 자동종료가 되는 현상이 있었는데 WSL의 문제로 판단된다. 

해결은 /mnt/.. 경로에 볼륨을 설정하지 않고 wsl내부의 경로로 설정을 해주었다. 
```
volumes:
  - /docker-practice/mysql-data:/var/lib/mysql
```