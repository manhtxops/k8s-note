# Phân quyền người dùng

![Untitled](Pha%CC%82n%20quye%CC%82%CC%80n%20ngu%CC%9Bo%CC%9B%CC%80i%20du%CC%80ng%2002bb0a497d1f4e1fba2bede90dd9ec7d/Untitled.png)

## **Vấn đề**

Hãy tưởng tượng ta gặp trường hợp như sau. Công ty ta có một Kubernetes Cluster với hai môi trường `pro` và `dev`. Với `pro` cho môi trường sản phẩm thực tế và `dev` dùng để phát triển sản phẩm. Trước giờ bạn chỉ làm một mình và có toàn bộ quyền để quản lý Kubernetes Cluster, mỗi lần Developer muốn làm gì đều phải thông qua bạn, kể cả xem logs. Sếp bạn thấy vậy hơi bất tiện nên kêu bạn tìm cách làm thế nào để mấy bạn Developer có thể tự liệt kê ứng dụng và xem logs ở dưới môi trường `dev`.

## **Giải pháp**

Ta dùng Role-based access control (RBAC) để giải quyết vấn đề trên.