# Kiến trúc hệ thống K8S

![Untitled](Kie%CC%82%CC%81n%20tru%CC%81c%20he%CC%A3%CC%82%20tho%CC%82%CC%81ng%20K8S%20b3892c89463b4bc988093833a0cffc69/Untitled.png)

Hệ thống Kubernetes gồm 2 phần chính:

- **Control Plane (Master Node)**: Xử lý các tác vụ quản lý, điều khiển của hệ thống.
- **Data Plane (Worker Node)**: Xử lý các work load của hệ thống. Các pod sẽ được tạo và chạy trên các Worker Node này.

**Master Node sẽ có 4 thành phần chính gồm:**

- **etcd:** Là một cơ sở dữ liệu dạng key-value có tính khả dụng và đồng nhất cao. Etcd là nơi K8S lưu trữ toàn bộ các thông tin cấu hình của hệ thống.
- **controller:** Là một tiến trình chạy nền trên các Master Node. Các tiến trình này chạy liên tục để điều tiết trạng thái của hệ thống Kubernetes. Trong K8S, controller là một vòng lặp điều khiển giám sát trạng thái của cluster được chia sẻ qua qua các api và thực hiện các thay đổi cần thiết để chuyển trạng của cluster tới trạng thái mong muốn.
- **kube api-server:** Đây là **core** của K8S Master, nó mở ra các HTTP API cho phép người dùng cuối cũng như các thành phần khác nhau trong chính K8S cluster có thể trao đổi thông tin với nhau. K8S API cho phép người dùng lấy thông tin về trạng thái của các đối tượng trong hệ thống như Pods, Namespaces, Services... Hầu hết các tác vụ sử dụng kube-api thông qua lệnh kubectl nhưng cũng có thể gọi trực tiếp REST API.
- **kube-scheduler:** Đây là service mặc định của K8S làm nhiệm vụ phân phối Pod sẽ được chạy trên node nào. Mỗi Container bên trong Pod có thể có những yêu cầu khác nhau, hoặc ngay các Pod cũng có yêu cầu khác nhau. Do đó nhiệm vụ của Scheduler là tìm kiếm các node thỏa mãn các điều kiện trên và lựa chọn node tối ưu nhất để chạy. Trong trường hợp không có node nào thỏa mãn các điều kiện đặt ra thì Pod sẽ ở trạng thái chưa được lên lịch thực hiện cho tới khi Scheduler tìm được node phù hợp.

**Worker Node sẽ có các thành phần chính gồm:**

- **kubelet**: Nó đóng vai trò như một "Node Agent" của K8s trên các Worker Node. Nhiệm vụ của nó để Worker Node được đăng ký và quản lý bởi cụm K8S cũng như là nhận nhiệm vụ triển khai các Pod (thường thông qua kube api-server) và đảm báo các container đó chạy ổn định. Lưu ý là kubelete không quản lý các container không được tạo bởi Kubernetes.
- **kube-proxy**: Kube-proxy là một network proxy chạy trên mỗi node trong K8S cluster, thực hiện một phần Kubernetes Service. Kube-proxy duy trình network rules trên các node. Những network rules này cho phép kết nối mạng đến các pods từ trong hoặc ngoài cluster.