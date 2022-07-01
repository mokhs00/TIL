
nginx 설치

``` shell
apt-get nginx
```

설정 파일 수정

``` shell
vi /etc/nginx/site-available/default
```

```bash
server {
        listen 사용할 포트번호;
				server_name example.com # 사용할 도메인이 있는 경우 작성
				root build디렉토리 경로
        index index.html
        location / {
					try_files $uri $uri/ /index.html; # 404로 하면 새로고침 시 404로 감
				}
}
```