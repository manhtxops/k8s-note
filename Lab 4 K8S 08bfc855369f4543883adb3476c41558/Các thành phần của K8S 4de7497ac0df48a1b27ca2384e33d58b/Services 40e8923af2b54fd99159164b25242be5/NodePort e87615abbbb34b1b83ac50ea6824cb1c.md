# NodePort

Đây là cách đầu tiên để expose Pod cho client bên ngoài có thể truy cập vào được Pod bên trong cluster. Giống như ClusterIP, NodePort cũng sẽ tạo endpoint có thể truy cập được bên trong cluster bằng IP và DNS, đồng thời nó sẽ sử dụng một port của toàn bộ worker node để client bên ngoài có thể giao tiếp được với Pod của chúng ta thông qua port đó. NodePort sẽ có range mặc định từ 30000 - 32767. 

![Untitled](NodePort%20e87615abbbb34b1b83ac50ea6824cb1c/Untitled.png)

![Untitled](NodePort%20e87615abbbb34b1b83ac50ea6824cb1c/Untitled%201.png)