# Kubernetes Pod: thành phần để chạy container

Pod là thành phần cơ bản nhất để deploy và chạy một ứng dụng, được tạo và quản lý bởi kubernetes. Pod được dùng để nhóm (group) và chạy một hoặc nhiều container lại với nhau trên cùng một worker node, những container trong một pod sẽ chia sẻ chung tài nguyên với nhau.

**Thông thường chỉ nên run Pod với 1 container**

![Untitled](Kubernetes%20Pod%20tha%CC%80nh%20pha%CC%82%CC%80n%20%C4%91e%CC%82%CC%89%20cha%CC%A3y%20container%20e79f3425a37c466dbdcc5ef6ab6e17bb/Untitled.png)

# Tại sao là lại dùng Pod để chạy container, sao không chạy container trực tiếp?

Kubernetes Pod như một wrapper của container, cung cấp cho chúng ta thêm nhiều chức năng để quản lý và chạy một container, giúp container của ta chạy tốt hơn là chạy container trực tiếp, như là group tài nguyên của container, check container healthy và restart, chắc chắn ứng dụng trong container đã chạy thì mới gửi request tới container đó, cung cấp một số lifecycle để ta có thể thêm hành động vào Pod khi Pod chạy hoặc shutdown, v...v... Và kubernetes sẽ quản lý Pod thay vì quản lý container trực tiếp

![Untitled](Kubernetes%20Pod%20tha%CC%80nh%20pha%CC%82%CC%80n%20%C4%91e%CC%82%CC%89%20cha%CC%A3y%20container%20e79f3425a37c466dbdcc5ef6ab6e17bb/Untitled%201.png)

Thường thì ta sẽ không chạy Pod trực tiếp như thế này, mà sẽ sử dụng các resource khác của kube để chạy Pod

## Câu lệnh tương tác với Pod:

1. Chạy file config của pod:
    
    <aside>
    💡 kubectl apply -f file_name.yaml
    
    </aside>
    
2. Kiểm tra pod
    
    <aside>
    💡 kubectl get pod
    
    </aside>
    
3. Expose port của pod
    
    <aside>
    💡 kubectl port-forward pod/hello-kube 3000:3000
    
    </aside>
    
4. Xóa pod bằng câu lệnh
    
    <aside>
    💡 kubectl delete pod hello-kube
    
    </aside>
    
5. List pod với labels
    
    <aside>
    💡 kubectl get pod --show-labels
    
    </aside>
    
6. Chọn chính xác cột label hiển thị với options -L 
    
    <aside>
    💡 kubectl get pod -L enviroment
    
    </aside>
    
7. List ra toàn bộ namespace
    
    <aside>
    💡 kubectl get ns
    
    </aside>
    
8. Chỉ định resource của namespace chúng ta muốn bằng cách thêm option --namespace vào
    
    <aside>
    💡 kubectl get pod --namespace namespace_name
    kubectl get pod -n namespace_name
    
    </aside>
    
9. Tạo namespace
    
    <aside>
    💡 kubectl create ns namespace_name
    
    </aside>
    
10. Xóa namespace bằng cách dùng câu lệnh delete, **chú ý là khi xóa namespace thì toàn bộ resource trong đó cũng sẽ bị xóa theo**
    
    <aside>
    💡 kubectl delete ns namespace_name
    
    </aside>
    

## **Tổ chức pod bằng cách sử dụng labels**

Dùng label là cách để chúng ta có thể phân chia các pod khác nhau tùy thuộc vào dự án hoặc môi trường. Ví dụ công ty của chúng ta có 3 môi trường là testing, staging, production, nếu chạy pod mà không có đánh label thì chúng ta rất khó để biết pod nào thuộc môi trường nào

![Untitled](Kubernetes%20Pod%20tha%CC%80nh%20pha%CC%82%CC%80n%20%C4%91e%CC%82%CC%89%20cha%CC%A3y%20container%20e79f3425a37c466dbdcc5ef6ab6e17bb/Untitled%202.png)

![Untitled](Kubernetes%20Pod%20tha%CC%80nh%20pha%CC%82%CC%80n%20%C4%91e%CC%82%CC%89%20cha%CC%A3y%20container%20e79f3425a37c466dbdcc5ef6ab6e17bb/Untitled%203.png)

Labels là một thuộc tính cặp key-value mà chúng ta gán vào resource ở phần metadata, ta có thể đặt tên key và value với tên bất kì.

## **Phân chia tài nguyên của kubernetes cluster bằng cách sử dụng namespace**

Ví dụ trong một dự án thì ta muốn tài nguyên của production phải nhiều hơn của testing, thì ta làm thế nào? Chúng ta sẽ dùng **namespace**

Namespace là cách để ta chia tài nguyên của cluster, và nhóm tất cả những resource liên quan lại với nhau, bạn có thể hiểu namespace như là một sub-cluster. 

Namespace default là namespace chúng ta đang làm việc với nó, khi ta sử dụng câu lệnh kubectl get để hiển thị resource, nó sẽ hiểu ngầm ở bên dưới là ta muốn lấy resource của namespace mặc định

Cách tổ chức namespace tốt là tạo theo **`<project_name>:<enviroment>`**. Ví dụ: mapp-testing, mapp-staging, mapp-production, kala-testing, kala-production.