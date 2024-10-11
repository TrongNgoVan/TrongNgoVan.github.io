---
layout: post
title: Deploy React với Docker, Nginx
date: 2021-03-11 10:31 +0700
---

# Mục tiêu

Trong bài viết này chúng ta sẽ tiến hành cấu hình môi trường để deploy ứng dụng React với Nginx trong ```Docker```.

# Tiến hành

## 1. Init project

Trước tiên để có thể deploy ứng dụng thì ta cần 1 project, ở đây mình sẽ bắt đầu với project cơ bản nhất của React.

```
npx create-react-app my-app
```

Sau khi init project xong, chúng ta sẽ có 1 cấu trúc dự án như sau

```
├── package.json
├── public
│   ├── ...
│   └── robots.txt
├── README.md
├── src
│   ├── ...
│   └── App.js
└── yarn.lock
```

## 2. Setup Docker

Tiếp đó ta thêm 1 vài file config sau

```
+   ├── docker-compose.yml
+   ├── Dockerfile
+   ├── .dockerignore
+   ├── .nginx
+   │   └── nginx.conf
    ├── package.json
    ├── public
    │   ├── ...
    │   └── robots.txt
    ├── README.md
    ├── src
    │   ├── ...
    │   └── App.js
    └── yarn.lock
```

### 2.1. ```.dockerignore```

Nó sẽ ignore các file chúng ta không muốn copy vào trong image, từ đó giúp cho giảm kích thước của image tối đa.

```
node_modules
npm-debug.log
build
.dockerignore
**/.git
**/.DS_Store
**/node_modules
```

### 2.2. Folder ```.nginx```

Đây sẽ folder để config ```nginx```

File ```nginx.conf```

{% highlight nginx %}
    server {    

    listen 80;

    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
        try_files $uri /index.html =404;
    }

    error_page 500 502 503 504 /50x.html;

    location = /50x.html {
        root /usr/share/nginx/html;
    }
    }
{% endhighlight %}

- ```listen 80```: ```nginx``` sẽ listen port 80 trong container để hiển thị app.
- ```try_files```: Đối với React sẽ là single page, tức là chỉ có 1 route duy nhất để hiển thị toàn bộ app. Với thứ tự ```try_files``` như trên nginx sẽ duyệt lần lượt theo thứ tự ```$uri > /index.html > 404(not found page)``` để kiểm tra, nếu k hợp lệ thì sẽ chuyển đến route tiếp theo để kiểm tra và hiển thị.
- ```error_page```: một vào route lỗi hiển thị mặc định.

### 2.3. File ```Dockerfile```

{% highlight docker %}
    # 1. For build React app
    FROM node:lts AS development
    # Set working directory
    WORKDIR /app
    #
    COPY package.json /app/package.json
    COPY package-lock.json /app/package-lock.json
    # Same as npm install
    RUN npm ci
    COPY . /app
    ENV CI=true
    ENV PORT=3000
    CMD [ "npm", "start" ]
    FROM development AS build
    RUN npm run build
    # 2. For Nginx setup
    FROM nginx:alpine
    # Copy config nginx
    COPY --from=build /app/.nginx/nginx.conf /etc/nginx/conf.d/default.conf
    WORKDIR /usr/share/nginx/html
    # Remove default nginx static assets
    RUN rm -rf ./*
    # Copy static assets from builder stage
    COPY --from=build /app/build .
    # Containers run nginx with global directives and daemon off
    ENTRYPOINT ["nginx", "-g", "daemon off;"]
{% endhighlight %}

### 2.4. Folder ```docker-compose.yml```

Để đơn giản cho việc run Docker và tích hợp với nhiều service khác, ta sẽ viết thêm docker-compose.yml

{% highlight docker %}
    version: "3.7"
    services:
    frontend:
        build:
        context: .
        container_name: frontend
        ports:
        - "3000:80" # map 80 in container => 3000 in local
{% endhighlight %}

## 3. Run Docker

- Start container
{% highlight docker %}

    $ docker-compose up -d
    Building frontend
    Sending build context to Docker daemon   1.49MB

    Step 1/17 : FROM node:lts AS development
    ---> 9153ee3e2ced
    Step 2/17 : WORKDIR /app
    ---> Using cache
    ---> a7909d92148a
    Step 3/17 : COPY package.json /app/package.json
    ---> 2e690dfe99b2
    Step 4/17 : COPY package-lock.json /app/package-lock.json
    ---> dd0132803f43
    .....
    Step 16/17 : COPY --from=build /app/build .
    ---> Using cache
    ---> 447488bdf601
    Step 17/17 : ENTRYPOINT ["nginx", "-g", "daemon off;"]
    ---> Using cache
    ---> 6372a67cf86f
    Successfully built 6372a67cf86f
    Successfully tagged react-nginx_frontend:latest
{% endhighlight %}

Sau khi run xong chúng ta xem ứng dụng ở [http://localhost:3000](http://localhost:3000)

- Stop container

{% highlight docker %}

    $ docker-compose down
    Stopping frontend ... done
    Removing frontend ... done
    Removing network react-nginx_default
{% endhighlight %}


# 3. Tổng kết
Trên đây là hướng dẫn cấu hình để triển khai 1 ứng dụng React, Nginx trong ```Docker```.

Các bạn có thể xem code tại đây [github](https://github.com/docker/awesome-compose/tree/master/react-nginx)
