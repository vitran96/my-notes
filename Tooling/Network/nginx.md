nginx is an HTTP and reverse proxy server, a mail proxy server, and a generic TCP/UDP proxy server, originally written by [[Igor Sysoev]]

# Sample config for [[static web]]

```nginx
server {
	listen [::]:4200;
	listen 4200;
	root /usr/share/nginx/html/apps;
	include mimes.types;
	index index.html index.htm;
	location / {
		try_files $uri $uri/ /index.html;
	}
}
```

# Sample config for [[API Gateway]]

```nginx
server {
	listen 8088;
	server_name example.com;

	location / {
		proxy_pass http://fe:4200;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $schema;
	}

	location /api/ {
		proxy_pass http://be:8000/;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header x-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $schema;
	}
}
```