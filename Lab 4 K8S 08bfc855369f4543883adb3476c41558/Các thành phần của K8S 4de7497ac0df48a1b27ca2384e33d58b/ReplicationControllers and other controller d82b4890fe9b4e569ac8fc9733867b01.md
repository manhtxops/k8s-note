# ReplicationControllers and other controller

Pod là thành phần cơ bản nhất để deploy application, nhưng trong thực tế ta sẽ không chạy pod trực tiếp, vì nó sẽ gặp nhiều hạn chế, mà chúng ta sẽ tạo những resource khác như ReplicationControllers hoặc ReplicaSets, và nó sẽ tự động tạo và quản lý pod

## **ReplicationControllers là gì?**

ReplicationControllers là một resource mà sẽ tạo và quản lý pod, và chắc chắn là số lượng pod nó quản lý không thay đổi và kept running. ReplicationControllers sẽ tạo số lượng pod bằng với số ta chỉ định ở thuộc tính replicas và quản lý pod thông qua labels của pod

![Untitled](ReplicationControllers%20and%20other%20controller%20d82b4890fe9b4e569ac8fc9733867b01/Untitled.png)

## **Tại sao ta nên dùng ReplicationControllers để chạy pod ?**

Chúng ta đã biết pod nó sẽ giám sát container và tự động restart lại container khi nó fail

![Untitled](ReplicationControllers%20and%20other%20controller%20d82b4890fe9b4e569ac8fc9733867b01/Untitled%201.png)

Vậy trong trường hợp toàn bộ worker node của chúng ta fail thì sẽ thế nào? pod nó sẽ không thể chạy nữa, và application của chúng ta sẽ downtime với người dùng

![Untitled](ReplicationControllers%20and%20other%20controller%20d82b4890fe9b4e569ac8fc9733867b01/Untitled%202.png)

Nếu chúng ta chạy cluster với hơn 1 worker node, RC sẽ giúp chúng ta giải quyết vấn đề này. Vì RC sẽ chắc chắn rằng số lượng pod mà nó tạo ra không thay đổi, nên ví dụ khi ta tạo một thằng RC với số lượng replicas = 1, RC sẽ tạo 1 pod và giám sát nó, khi một thằng worker node die, nếu pod của thằng RC quản lý có nằm trong worker node đó, thì lúc này thằng RC sẽ phát hiện ra là số lượng pod của nó bằng 0, và nó sẽ tạo ra thằng pod ở một worker node khác để đạt lại được số lượng 1

![Untitled](ReplicationControllers%20and%20other%20controller%20d82b4890fe9b4e569ac8fc9733867b01/Untitled%203.png)

Dưới đây là hình minh họa cách hoạt động của RC

![Untitled](ReplicationControllers%20and%20other%20controller%20d82b4890fe9b4e569ac8fc9733867b01/Untitled%204.png)

Sử dụng ReplicationControllers để chạy pod sẽ giúp ứng dụng của chúng ta luôn luôn availability nhất có thể. Ngoài ra ta có thể tăng performance của ứng dụng bằng cách chỉ định số lượng replicas trong RC để RC tạo ra nhiều pod chạy cùng một version của ứng dụng.

Ví dụ ta có một webservice, nếu ta chỉ deploy một pod để chạy ứng dụng, thì ta chỉ có 1 container để xử lý request của user, nhưng nếu ta dùng RC và chỉ định replicas = 3, ta sẽ có 3 pod chạy 3 container của ứng dụng, và request của user sẽ được gửi tới 1 trong 3 pod này, giúp quá trình xử lý của chúng ta tăng gấp 3 lần

![Untitled](ReplicationControllers%20and%20other%20controller%20d82b4890fe9b4e569ac8fc9733867b01/Untitled%205.png)

Cấu trúc của một file config RC sẽ gồm 3 phần chính như sau:

- label selector: sẽ chỉ định pod nào sẽ được RC giám sát
- replica count: số lượng pod sẽ được tạo
- pod template: config của pod sẽ được tạo

![Untitled](ReplicationControllers%20and%20other%20controller%20d82b4890fe9b4e569ac8fc9733867b01/Untitled%206.png)

Hoạt động của thằng RC được minh họa như hình sau:

![Untitled](ReplicationControllers%20and%20other%20controller%20d82b4890fe9b4e569ac8fc9733867b01/Untitled%207.png)

## Các câu lệnh tương tác với RC

1. Xem rc của chúng ta đã chạy thành công hay chưa
    
    <aside>
    💡 kubectl get rc
    
    </aside>
    
2. Xóa RC
    
    <aside>
    💡 kubectl delete rc rc_name
    
    </aside>
    

## **Thay đổi template của pod**

Bạn có thể thay đổi template của pod và cập nhật lại RC, nhưng nó sẽ không apply cho những thằng pod hiện tại, muốn pod của bạn cập nhật template mới, bạn phải xóa hết pod để RC tạo ra pod mới, hoặc xóa RC và tạo lại