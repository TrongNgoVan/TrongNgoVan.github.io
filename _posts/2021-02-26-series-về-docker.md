---
layout: post
title: Series về  Docker
date: 2021-02-26 09:58 +0700
categories: [devops]
tags: [docker]
---

# Series về Docker

## install, setup

- docker
- docker-compose (phân quyền)

## Các khái niệm cần chú ý

- container, images,
- Dockerfile, dockerignore
- docker-compose

## Thực hành

- Cấu hình Docker cho project Reactjs
-

## Các command thông dụng

- remove all images <br>
  `docker rmi $(docker images --filter 'dangling=true' -q --no-trunc)`
- bash <br>
  ` docker run --name XXX --rm -i -t ubuntu bash`
