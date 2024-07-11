# ExternalName

ExternalName Service là một trường hợp đặc biệt, nó không sử dụng selector mà thay vào đó sử dụng một DNS name. Ta sẽ xem một ví dụ khai báo ExternalName Service như sau:

![Untitled](ExternalName%20be9723d63ecf4f24a8f7fba164186d45/Untitled.png)

Như vậy khi kết nối tới service **my-database-svc** thì DNS của k8s sẽ trả về một bản ghi để kết nối tới [**my.database.example.com**](http://my.database.example.com/). Loại service này có thể ứng dụng để khai báo kết nối tới các dịch vụ bên ngoài k8s.