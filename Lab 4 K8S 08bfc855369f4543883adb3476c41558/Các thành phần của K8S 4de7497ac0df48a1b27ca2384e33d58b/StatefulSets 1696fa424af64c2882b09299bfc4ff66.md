# StatefulSets

**Deploying replicated stateful applications**

> Stateless application là một ứng dụng mà không có lưu trữ trạng thái của chính nó, hoặc là không có lưu trữ dữ liệu mà cần persistent storage. Ví dụ là một web server API mà không có lưu trữ hình ảnh, hoặc session login của user, thì đó là một stateless apps, bởi vì cho dù ta có xóa ứng dụng của ta và tạo lại nó bao nhiêu lần thì cũng không ảnh hưởng tới dữ liệu của người dùng. Bởi vì dữ liệu của ta được lưu trữ thông qua database, web server API chỉ kết nối với database và lưu trữ dữ liệu, chứ nó không có dữ liệu của chính nó. Một ví dụ nữa là command line app, nó không cần phải lưu trữ dữ liệu gì cả, tất cả những gì nó cần là xuất ra kết quả và không cần phải lưu lại kết quả đó. Những đặc tính của stateless app sẽ giúp nó dễ dàng scale hơn.
> 

> Stateful application thì yêu cầu có trạng thái (state) của chính nó, và cần lưu lại state đó, hoặc yêu cầu phải có lưu trữ dữ liệu mà cần persistent storage, dữ liệu này sẽ được sử dụng bởi client và các app khác. Ví dụ như là database, nó sẽ có dữ liệu riêng của nó.
> 

Ở trong kubernetes ta có thể deploy một stateful application bằng cách tạo Pod và config volume cho Pod, hoặc dùng PersistentVolumeClaim. Nhưng ta chỉ có thể tạo một single instance của Pod mà kết nối tới PersistentVolumeClaim đó. Vậy thì có thể dùng ReplicaSet để tạo replicated stateful app không? Cái ta muốn là sẽ tạo ra nhiều replicas của Pod, và mỗi Pod ta sẽ dùng một PersistentVolumeClaim riêng, để chạy được một ứng dụng distributed data store.

## **Hạn chế của việc sử dụng ReplicaSet để tạo replicated stateful app**

Vì ReplicaSet tạo nhiều pod replicas từ một Pod template, nên những Pod được replicated đó không khác với những Pod khác ngoại trừ tên và IP. Nếu ta config volume trong Pod template thì tất cả các Pod được replicated đều lưu trữ dữ liệu chung một storage.

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled.png)

Nên ta không thể sử dụng một ReplicaSet rồi set thuộc tính replicas của nó để chạy một ứng dụng distributed data store được. Ta cần phải sử dụng cách khác.

## **Tạo nhiều ReplicaSet chỉ có một Pod mỗi ReplicaSet**

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled%201.png)

Ta có thể dụng cách này để deploy một ứng dụng distributed data store. Nhưng đây không phải là cách tốt, ví dụ khí ta muốn scale ứng dụng của ta lên thì ta sẽ làm thế nào? Ta chỉ có cách là tạo thêm một ReplicaSet bằng tay, công việc này không tự động chút nào. Ta chọn kubernetes để chạy ứng dụng là vì muốn mọi thứ như scale đều được tự động và dễ dàng nhất.

## **Cung cấp stable identity cho mỗi Pod**

Đối với statefull application, ta cần định danh cho mỗi Pod, vì Pod có thể bị xóa và tạo lại bất cứ lúc nào, khi ReplicaSet thay thế một thằng Pod cũ bằng Pod mới thì Pod mới tạo ra sẽ có tên khác và IP khác. Cho dù dữ liệu ta vẫn còn đó và giống như Pod cũ, nhưng đối với một số ứng dụng, khi ta tạo Pod mới mà nó có một network identity mới (như địa chỉ IP) thì sẽ sinh ra nhiều vấn đề. Nên ta cần phải dùng Service để identity IP cho Pod, ta có bao nhiêu ReplicaSet thì ta sẽ cần tạo bấy nhiêu Service tương ứng.

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled%202.png)

Khi ta có thêm Service, lúc này ta muốn scale lên, bên cạnh việc phải tạo một ReplicaSet mới, bây giờ ta cần phải tạo thêm một Service mới cho thằng ReplicaSet tương ứng nữa, gấp đôi công việc phải làm bằng tay.

Thì để giải quyết những vấn đề trên, có thể dễ dạng tạo nhiều replicated của Pod, mỗi thằng sẽ có một định danh riêng, và dễ dạng tự động scale mà không cần ta phải làm bằng tay quá nhiều, giúp ta dễ hơn trong việc tạo một ứng dụng distributed data store. Kubernetes cung cấp cho chúng ta một resource tên là StatefulSet.

## **StatefulSets**

Giống như ReplicaSet, StatefulSet là một resource giúp chúng ta chạy nhiều Pod mà cùng một template bằng cách set thuộc tính replicas, nhưng khác với ReplicaSet ở chỗ là Pod của StatefulSet sẽ được định danh chính xác và mỗi thằng sẽ có một stable network identity của riêng nó.

Mỗi Pod được tạo ra bởi StatefulSet sẽ được gán với một index, index này sẽ được sử dụng để định danh cho mỗi Pod. Và tên của Pod sẽ được đặt theo kiểu `<statefulset name>-<index>`, chứ không phải random như của ReplicaSet.

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled%203.png)

## **Cách StatefulSets thay thế một Pod bị mất**

Khi một Pod mà được quản lý bởi một StatefulSets bị mất (do bị ai đó xóa đi), thằng StatefulSets sẽ tạo ra một Pod mới để thay thế thằng cũ tương tự như cách làm của ReplicaSet, nhưng thằng Pod được tạo mới này sẽ có tên và hostname giống y như thằng cũ.

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled%204.png)

Còn thằng ReplicaSet thì sẽ tạo ra thằng Pod mới hoàn toàn khác với thằng cũ.

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled%205.png)

## **Cách StatefulSets scale Pod**

Khi ta scale up Pod trong StatefulSets, nó sẽ tạo ra một Pod mới được đánh index là số tiếp theo của index hiện tại. Ví dụ StatefulSets đang có replicas bằng 2, sẽ có 2 Pod là `<pod-name>-0`,

`<pod-name>-1`, khi ta scale up Pod lên bằng 3, Pod mới được tạo ra sẽ có tên là `<pod-name>-2`.

Tương tự như vậy với scale down, nó sẽ xóa Pod với index lớn nhất. Đối với StatefulSets thì khi ta scale up và scale down thì ta có thể biết chính xác tên của Pod sẽ được tạo ra hoặc xóa đi.

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled%206.png)

## **Cung cấp storage riêng biệt cho mỗi Pod**

Tới đây thì chúng ta đã biết cách StatefulSets định danh cho mỗi thằng Pod, vậy còn storage thì sao? Mỗi Pod của chúng ta cần có một storage của riêng nó, và khi ta scale down số lượng Pod và scale up lại thì thằng Pod tạo ra mà có index giống với thằng cũ thì vẫn giữ nguyên storage của nó như không phải tạo ra một thằng storage khác.

Thằng StatefulSets làm được việc đó bằng cách tách storage ra khỏi Pod bằng cách sử dụng [PersistentVolumeClaims](https://viblo.asia/p/kubernetes-series-bai-7-persistentvolumeclaims-tach-pod-ra-khoi-kien-truc-storage-ben-duoi-6J3Zgyeq5mB). StatefulSets sẽ tạo ra PersistentVolumeClaims cho mỗi Pod và gắn nó vào cho từng Pod tương ứng.

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled%207.png)

Khi ta scale up Pod trong StatefulSets, thì sẽ có một Pod và một PersistentVolumeClaims mới được tạo ra, nhưng khi ta scale down, thì chỉ có thằng Pod bị xóa đi, thằng PersistentVolumeClaims vẫn giữ ở đó và không bị xóa. Để khi ta scale up lại thì thằng Pod vẫn được gắn đúng với PersistentVolumeClaims trước đó để dữ liệu của nó vẫn được giữ nguyên.

![Untitled](StatefulSets%201696fa424af64c2882b09299bfc4ff66/Untitled%208.png)

## **Tạo một StatefulSets**