# Một pod được tạo trên K8S như thế nào ?

**Bài toàn đặt ra là tôi muốn tạo một Pod chạy trên K8S Cluster, ta sẽ xem K8S xử lý như thế nào để thực hiện yêu cầu trên**.

Dùng lệnh kubectl với cú pháp như sau để tạo một pod chạy nginx:

```bash
kubectl run --generator=run-pod/v1 nginx --image=nginx --restart="Always"
```

**Bước 1: kubectl --> api-server --> etcd**

kubectl sẽ kết nối tới **kube api-server** và nói với kube api-server rằng tôi muốn tạo một Pod mới có tên là **nginx**. 

Lúc này kube api-server đã nhận thông tin yêu cầu, và việc tiếp theo nó sẽ thực hiện ghi thông tin yêu cầu đó vào **etcd**. 

Có thể hiểu giống như bạn đang order một món ăn và api-server như một người quản lý nhận thông tin order và ghi vào giấy vậy. 

Khi ghi dữ liệu thành công vào etcd, etcd sẽ trả lời api-server và api-server trả về kết quả cho kubectl rằng pod đã được tạo (Pod created). 

Tuy nhiên thực tế tại thời điểm này chưa có Pod nào được tạo cả.

**Bước 2: scheduler --> api-server**

Tiếp đến sẽ tới nhiệm vụ của Scheduler. Scheduler sẽ định kỳ hỏi lại api-server rằng "Hey, có yêu cầu gì mới cần thực hiện không?" và api-server sẽ trả lời Scheduler rằng có một request tạo mới một Pod tên là **nginx**, hãy lên lịch thực hiện nó đi. 

Scheduler lúc này sẽ thực hiện kiểm tra tất cả các node đang sẵn sàng và chọn ra một node tối ưu nhất (ví dụ node1) có thể chạy Pod trên và trả kết quả về cho api-server.

**Bước 3: api-server --> etcd**
Khi nhận được kết quả của Scheduler, api-server sẽ ghi thông tin vào etcd, hiểu rằng Pod nginx sẽ cần được tạo trên node1.

**Bước 4: kubelet --> api-server**

kubelet định kỳ kết nối với api-server để cập nhật trạng thái node mà nó đang quản lý, trạng thái các Pod đang chạy trên node đó cũng như nhận các yêu cầu mới từ api-server. Hiểu đơn giản thì kubelet trao đổi với api-server 2 câu: "Hey api-server tao đang chạy 5 pod và tất cả đều đang running ok không có vấn đề gì cả, tao cũng đang sống khỏe" "Mày có việc gì mới cho tao không?" Lúc này api-server trả lời "Có, tao cần mày chạy một Pod mới với thông tin như thế này".

**Bước 5: kubelet --> CRI**

kubelet đã nhận thông tin từ api-server, nó làm việc với CRI (*CRI là viết tắt của cụm từ **Container Runtime Interface** đảm nhiệm vai trò duy trì hoạt động của container*) để tạo một Pod mới theo yêu cầu (CRI có thể là Docker hoặc ContainerD)

**Bước 6: controller manager**

Giờ thì Pod đã được tạo xong, tuy nhiên lưu ý rằng trong lệnh tạo Pod ta đã set tham số **--restart="Always"** nghĩa là nếu Pod này vì lý do gì mà bị down thì cần phải tạo một Pod khác thay thế. Vậy làm sao để làm được việc đó? Câu trả lời là **controller manager**.

Controller định kỳ sẽ gọi api-server để biết trạng thái hiện tại (current state) và trạng thái mong muốn (desired state). Trạng thái mong muốn là có 1 Pod nginx chạy trên node1, còn hiện tại thì không có Pod nginx nào đang chạy --> Controller Manager sẽ yêu cầu tạo lại Pod nginx để đảm bảo trạng thái hiện tại phải giống với trạng thái mong muốn.