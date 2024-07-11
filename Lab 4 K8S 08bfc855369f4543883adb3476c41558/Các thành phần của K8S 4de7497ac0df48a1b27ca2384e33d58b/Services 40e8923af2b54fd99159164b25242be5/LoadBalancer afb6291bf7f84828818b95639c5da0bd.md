# LoadBalancer

Khi bạn chạy kubernetes trên cloud, nó sẽ hỗ trợ LoadBalancer Service, nếu bạn chạy trên môi trường không có hỗ trợ LoadBalancer thì bạn không thể tạo được loại Service này. Khi bạn tạo LoadBalancer Service, nó sẽ tạo ra cho chúng ta một public IP, mà client có thể truy cập Pod bên trong Cluster bằng địa chỉ public IP này. 

Khi chạy trên k8s-onprem thì cơ bản LoadBalancer Service không khác gì NodePort Service.

![Untitled](LoadBalancer%20afb6291bf7f84828818b95639c5da0bd/Untitled.png)