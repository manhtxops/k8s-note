# Deployment

Một resource giúp chúng ta trong quá trình CD, cập nhật một phiên bản mới của ứng dụng một cách dễ dàng mà không gây ra ảnh hưởng nhiều tới client, là **Deployment**.

Trước khi nói về Deploymnet resource, ta sẽ xem qua nếu ta update một ứng dụng mà dùng những resource khác sẽ gặp những hạn chế gì.

## **Cập nhật một ứng dụng đang chạy trong pods**

Bắt đầu với một ví dụ là ta đang có một ứng dụng đang chạy, ta deploy nó bằng ReplicaSet, với replicas = 3, nó sẽ chạy 3 Pod đằng sau, và ta deploy một Service để expose traffic của nó ra cho client bên ngoài.

![Untitled](Deployment%20c1ab64ef3f3144e7a2be3e19e488ef87/Untitled.png)

Bây giờ các dev trong team đã viết xong tính năng mới, ta build lại image với code mới, và ta muốn update lại các Pod đang chạy này với image mới. Ta sẽ làm như thế nào? Thì trong quá trình deploy, sẽ có rất nhiều chiến lược để làm, nhưng có 2 cách thông dụng nhất là: **Recreate and RollingUpdate**

### **Recreate**

Ở cách deploy này, đầu tiên là sẽ xóa toàn bộ phiên bản (version) cũ của ứng dụng trước, sau đó ta sẽ deploy một version mới lên. Đối với kubernes thì đầu tiên ta sẽ cập nhật Pod template của ReplicaSet, sau đó ta xóa toàn bộ Pod hiện tại, để ReplicaSet tạo ra Pod với image mới.

![Untitled](Deployment%20c1ab64ef3f3144e7a2be3e19e488ef87/Untitled%201.png)

Với cách deploy này thì quá trình deploy quá dễ dàng, nhưng ta sẽ gặp một vấn đề rất lớn, đó là ứng dụng của chúng ta sẽ downtime với client, client không thể request tới ứng dụng của ta trong quá trình version mới được deploy lên.

### **RollingUpdate**

Ở cách này, ta sẽ deploy từng version mới của ứng dụng lên, chắc chắn rằng nó đã chạy, ta dẫn request tới version mới của ứng dụng này, lặp lại quá trình này cho tới khi toàn bộ version mới của ứng dụng được deploy và version cũ đã bị xóa. Đối với kubernetes, ta sẽ lần lượt xóa từng Pod và ReplicaSet sẽ tạo Pod mới cho ta.

![Untitled](Deployment%20c1ab64ef3f3144e7a2be3e19e488ef87/Untitled%202.png)

## **Lùi lại phiên bản trước của ứng dụng**

Thì ở đây chúng ta sẽ có cách là fix bug, build image mới, rồi thực hiện lại cách depoy là Recreate hoặc RollingUpdate đối với những lỗi không cần gấp lắm và không lớn lắm. Còn đối với lỗi bự và ảnh hưởng nhiều tới client thì ta thường sẽ revert lại code trên git, sau đó ta build image, thực hiện deploy lại.

Đối với kubernetes, chúng ta dùng container chạy ứng dụng thì chỉ cần update lại version của image trong Pod template bằng image trước đó, rồi dùng cách Recreate hoặc RollingUpdate để deploy lại Pod là xong, không cần revert code hoặc fixbug ngay lập tức. Dù là cách nào thì cũng sẽ tốn nhiều thời gian để thực hiện và chạy CI/CD lại.

Để giải quyết những vấn đề trên thì kubernetes cung cấp một resource là **Deployment**.

# **Deployment là gì?**

Deployment là một resource của kubernetes giúp ta trong việc cập nhật một version mới của úng dụng một cách dễ dàng, nó cung cấp sẵn 2 strategy để deploy là Recreate và RollingUpdate, tất cả đều được thực hiện tự động bên dưới, và các version được deploy sẽ có một histroy ở đằng sau, ta có thể rollback and rollout giữa các phiên bản bất cứ lúc nào mà không cần chạy lại CI/CD.

Khi ta tạo một Deployment, nó sẽ tạo ra một ReplicaSet bên dưới, và ReplicaSet sẽ tạo Pod. Luồn như sau **Deployment tạo và quản lý ReplicaSet -> ReplicaSet tạo và quản lý Pod -> Pod run container**.

![Untitled](Deployment%20c1ab64ef3f3144e7a2be3e19e488ef87/Untitled%203.png)

Ta cũng có thể chọn deploy strategy ta muốn cực kì đơn giản bằng cách chỉ định trong thuộc tính **strategy** của Deployment.

Các câu lệnh Deployment:

1. Để update lại ứng dụng trong Pod với Deployment

<aside>
💡 kubectl set image deployment <deployment-name> <container-name>=<new-image>

</aside>

1. Kiểm tra qua trình update đã xong chưa

<aside>
💡 kubectl rollout status deploy <deployment-name>

</aside>

1. Kiểm tra lịch sử các lần ứng dụng của chúng ta đã cập nhật

<aside>
💡 kubectl rollout history deploy <deployment-name>

</aside>

1. Ta đang ở revision là 3, ta muốn quay lại revision là 2, lúc ứng dụng của ta chưa có lỗi

<aside>
💡 kubectl rollout undo deployment <deployment-name> --to-revision=2

</aside>

## **Cách Deployment update Pod**

Ở dưới kubernetes, khi ta thay đổi image của Deployment, nó sẽ tạo ra một ReplicaSet khác, và ReplicaSet đó sẽ giữ template Pod mới, và các Pod mới sẽ được tạo ra bởi ReplicaSet.

![Untitled](Deployment%20c1ab64ef3f3144e7a2be3e19e488ef87/Untitled%204.png)

Và ReplicaSet cũ sẽ không bị xóa, mà trong trường này thuộc tính **replicas** của nó sẽ được cập nhật lại là 0. Vì sao ReplicaSet cũ không bị xóa thì nó sẽ có vai trò là ở trong quá trình mà ta update version mới của ứng dụng, và ta phát hiện version mới nó bị lỗi, ta muốn rollback lại version trước, thì ReplicaSet cũ của ta vẫn ở đó, ta sẽ rollback lại dễ dàng hơn.

## **Rollback lại version trước khi version mới của ứng dụng bị lỗi**

Đây là minh họa của revision history.

![Untitled](Deployment%20c1ab64ef3f3144e7a2be3e19e488ef87/Untitled%205.png)

Về revision thì mặc định nó sẽ lưu là 10, ta có thể điều chỉnh số lượng history bạn muốn lưu lại bằng thuộc tính **revisionHistoryLimit** của Deployment

![Untitled](Deployment%20c1ab64ef3f3144e7a2be3e19e488ef87/Untitled%206.png)