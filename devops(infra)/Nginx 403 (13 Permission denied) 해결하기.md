# 개요

nginx 설정 중에 설정한 directory를 찾지 못하는 것으로 판단되어 log를 확인해보니 `403 (13: Permission denied)` 에러가 발생하였고, 이를 해결하는 방법을 기록합니다.


# error 발생 상황
 
```
server {
        listen 80;
        
        root /xxx/xxx/build;
        index index.html;

        server_name _;

        location / {
                try_files $uri $uri/ /index.html;
        }
}
```

`/etc/nginx/sites-available/default`에 위와 같이 설정을 해두고 해당 포트로 접속해보니 500 Error와 함께 페이지를 찾을 수 없었다.

그래서 바로 log를 확인해보았고, `/var/log/nginx/error.log`에서 다음과 같은 로그를 확인할 수 있었다.

`"/xxx/xxx/build/index.html" failed (13: Permission denied)`

log를 보아서는 해당 경로에 권한이 없어 접근하지 못 하는 것으로 판단했고, 다음과 같이 해결했다.

# 해결 하기

해결방법은 다음과 같다.

- nginx에서 root로 설정한 디렉토리 경로의 **권한**(유저 그룹)을 확인한다.
- 해당 유저 그룹을 `/etc/nginx/nginx.conf
` 에서 일치시킨다.


필자의 경우에는 해당 경로 권한 유저 그룹이 `root`로 되어 있었고 
`vi /etc/nginx/nginx.conf` 명령어를 통해 vi 편집기로 다음과 같이 user를 변경해주는 것으로 해결하였다.


`/etc/nginx/nginx.conf`
``` conf
#user www-data;
user root;
```


# reference

- nginx 권장 설정 : https://opentutorials.org/module/384/4530
- https://blog.whitelife.co.kr/137
- https://ganbarujoy.tistory.com/113
