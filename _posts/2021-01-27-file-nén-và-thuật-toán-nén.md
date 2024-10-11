---
layout: post
title: File nén và thuật toán Nén File
categories: [tech, algorithm]
tags: [file, compression]
date: 2021-01-27 23:42 +0700
---

# File nén

=> Vậy File nén là gì? Làm sao lại có thể giảm được kích thước của file trong khi đó vẫn bảo toàn được
dữ liệu hoặc ở mức chấp nhận được ?

- Nén File (compression) là quá trình giảm thiểu kích thước của files, sau đó gộp cái files lại thành 1 file duy nhất.

# Thuật toán

## Nén không mất dữ liệu (Lossless)

Thuật toán này bảo toàn các dữ liệu gốc, dữ liệu ban đầu của file. Nó sẽ loại bỏ đi những dữ liệu dư
thừa hoặc mã hóa, biểu diễn dữ liệu theo kiểu khác từ đó làm giảm kích thước của file. Thường được
sử dụng cho các định dạng như văn bản, số liệu,...

- `Run-length encoding`
- `Huffman coding`

## Nén mất dữ liệu (Lossy)

Thuật toán này sẽ loại bỏ 1 phần của dữ liệu đến mức chấp nhận được. Thường được dùng để nén các tập
tin đã phương tiện như: ảnh, mp3, mp4,...
- `JPEG`
- `AIC`
- `ACV`
