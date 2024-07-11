# ReplicaSets

Đây là một resource tương tự như RC, nhưng nó là một phiên bản mới hơn của RC và sẽ được sử dụng để thay thế RC. Chúng ta sẽ dùng ReplicaSets (RS) để deploy pod thay vì dùng RC

## Các câu lệnh tương tác với RS

1. Kiểm tra RS có chạy thành công hay không

<aside>
💡 kubectl get rs

</aside>

1. Xóa RS

<aside>
💡 kubectl delete rs hello-rs

</aside>

## **So sánh ReplicaSets và ReplicationController**

RS và RC sẽ hoạt động tương tự nhau. Nhưng RS linh hoạt hơn ở phần label selector, trong khi label selector thằng RC chỉ có thể chọn pod mà hoàn toàn giống với label nó chỉ định, thì thằng RS sẽ cho phép dùng một số expressions hoặc matching để chọn pod nó quản lý.

Ví dụ, thằng RC không thể nào match với pod mà có env=production và env=testing cùng lúc được, trong khi thằng RS có thể, bằng cách chỉ định label selector như **env=*** . Ngoài ra, ta có thể dùng **operators** với thuộc tính **matchExpressions** như sau:

<aside>
💡 selector:
    matchExpressions:
        - key: app
          operator: In
          values:
          - kubia

</aside>