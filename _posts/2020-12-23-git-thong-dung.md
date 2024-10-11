---
layout: post
title: Những câu lệnh Git thông dụng.
categories:
  - git
tags:
  - git-command
date: 2020-12-23 14:35 +0700
---

# Tổng hợp những câu lệnh Git hay dùng (update dần dần).

<hr />

## 1. Khi muốn ignore 1 file, folder sau khi đã push lên remote

VD: Khi lỡ push cả `node-modules` lên trên remote, và muốn nó bay màu ngay lập tức.

- Sửa file `.gitignore` <br/>
  Nếu chưa có file .gitignore thì thêm mới nó. Rồi sau đó thêm file, folder mà ae muốn ignore vào.
    <script src="https://gist.github.com/phamthainb/1b572a199208d9bb7eba6c8f303a17e6.js"></script>

- Run `git rm -r --cached .`

  - `rm --cached .` xóa các file đc chỉ định khỏi `index`. Ở đây mình để `.` sẽ thực thi trên tất cả các file. Và khi đó những file, folder nào ở trong `.gitignore` sẽ đc bỏ qua.
  - `-r` lặp đệ quy các file bên trong thư mục.

- Run

  ```
  git add .
  git commit -m 'clear git cache'
  git push
  ```

  - add lại tất cả các file và push lên remote

- Kiểm tra lại trên remote xem file đã bay màu chưa.

## 2. Xóa lịch sử của file, folder

```
- git filter-branch --force --index-filter "git rm --cached --ignore-unmatch PATH-TO-FILE" --prune-empty --tag-name-filter cat -- --all
- git push origin --force --all
```
- Dùng `filter-branch` để xóa lịch sử commit, push của file, fodler.








