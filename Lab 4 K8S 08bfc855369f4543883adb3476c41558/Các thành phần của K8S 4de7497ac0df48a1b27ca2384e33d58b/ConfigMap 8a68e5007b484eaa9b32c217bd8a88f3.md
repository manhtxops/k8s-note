# ConfigMap

Đây là một resource giúp chúng ta tách configuration ra riêng. Với giá trị sẽ được định nghĩa theo kiểu key/value pairs ở thuộc tính data, như sau:

![Untitled](ConfigMap%208a68e5007b484eaa9b32c217bd8a88f3/Untitled.png)

Và giá trị này sẽ được truyền vào bên trong container như một env. Và vì ConfigMap nó là một resource riêng lẻ, ta có thể sử dụng nó lại cho nhiều container khác nhau. Sử dụng ConfigMap là cách ta tách khỏi việc phải viết cấu hình env bên trong config container của Pod.

![Untitled](ConfigMap%208a68e5007b484eaa9b32c217bd8a88f3/Untitled%201.png)

## **Dùng ConfigMap để truyền cấu hình dạng file vào trong container thông qua volume config**

![Untitled](ConfigMap%208a68e5007b484eaa9b32c217bd8a88f3/Untitled%202.png)

![Untitled](ConfigMap%208a68e5007b484eaa9b32c217bd8a88f3/Untitled%203.png)

![Untitled](ConfigMap%208a68e5007b484eaa9b32c217bd8a88f3/Untitled%204.png)

Nó sẽ mount file my-nginx-config.conf vào /etc/nginx/conf.d