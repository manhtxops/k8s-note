# Volumn

# **Volume là gì?**

Volume hiểu đơn giản chỉ là một mount point từ hệ thống file của server vào bên trong container.

Tại sao ta cần volume thì đối với container, những thứ ta ghi vào filesystem của nó thì chỉ tồn tại khi container còn chạy. Khi một thằng Pod bị xóa và tạo lại, container mới sẽ được tao ra, lúc này thì những thứ ta ghi ở container trước sẽ bị mất đi. Nếu ta muốn giữ lại những dữ liệu đó thì ta phải sử dụng volume.

Trong kubernetes thì sẽ có những loại volume như sau:

- emptyDir
- hostPath
- gitRepo
- nfs
- gcePersistentDisk, awsElasticBlockStore, azureDisk **(cloud storage)**
- cinder, cephfs, iscsi, flocker, glusterfs, quobyte, rbd, flexVolume, vsphereVolume, photonPersistentDisk, scaleIO
- configMap, secret, downwardAPI
- PersistentVolumeClaim

Những loại volume trên được phân chia thành 3 dạng chính:

- Volume dùng để chia sẻ dữ liệu giữa các container trong Pod
- Volume đính kèm vào trong filesystem một node
- Volume đính kèm vào cluster và các node khác nhau có thể truy cập

Chúng ta không cần phải nhớ hết các loại volume, xài tới đâu thì ta google tìm kiếm tới đó. Ta chỉ cần nhớ một vài loại hay sử dụng nhất là **emptyDir, hostPath, cloud storage, PersistentVolumeClaim**. 

## **Sử dụng emptyDir volume để share data giữa các containers**

emptyDir là loại volume đơn giản nhất, nó sẽ tạo ra một empty directory bên trong Pod, các container trong một Pod có thể ghi dữ liệu vào bên trong nó. Volume chỉ tồn tại trong một lifecycle của Pod, dữ liệu trong loại volume này chỉ được lưu trữ tạm thời và sẽ mất đi khi Pod bị xóa. Ta dùng loại volume này khi ta chỉ muốn các container có thể chia sẻ dữ liệu lẫn nhau và không cần lưu trữ dữ liệu lại. Ví dụ là dữ liệu log từ một thằng container chạy web API, và ta có một thằng container khác sẽ truy cập vào log đó để xử lý log.

## **Sử dụng gitRepo để clone git repository vào container**

**gitRepo không còn được sử dụng ở phiên bản 1.25**

gitRepo là loại volume cũng giống emptyDir là sẽ tạo một empty folder, và sau đó nó sẽ clone code của git repository vào folder này.

![Untitled](Volumn%208ea0b377417a49f3ad08aa19b0b6ec4c/Untitled.png)

## **Sử dụng hostPath để truy cập filesystem của worker node**

hostPath là loại volume sẽ tạo một mount point từ Pod ra ngoài filesystem của node. Đây là loại volume đầu tiên ta nói mà có thể dùng để lưu trữ persistent data. Dữ liệu lưu trong volume này chỉ tồn tại trên một worker node và sẽ không bị xóa đi khi Pod bị xóa.

![Untitled](Volumn%208ea0b377417a49f3ad08aa19b0b6ec4c/Untitled%201.png)

![Untitled](Volumn%208ea0b377417a49f3ad08aa19b0b6ec4c/Untitled%202.png)

Đối với loại volume này thì Pod của ta cần phải được tạo đúng worker node thì ta mới có được dữ liệu trước đó, nếu Pod của ta được tạo ở một worker node khác thì khi đó Pod sẽ không có dữ liệu cũ, do dữ liệu vẫn nằm ở worker node cũ. Loại volume này ta không sử dụng nó cho việc lưu trữ persistent data hoàn toàn được. Cái ta muốn là dù Pod được tạo ở worker node nào thì dữ liệu của ta vẫn có, để mount được vào trong container.

## **Sử dụng cloud storage để lưu trữ persistent data**

Loại volume này chỉ được hỗ trợ trên các nền tảng cloud, giúp ta lưu trữ persistent data, kể cả khi Pod được tạo ở các worker node khác nhau, dữ liệu của ta vẫn tồn tại cho container. 3 nền tảng cloud mà phổ biến nhất là AWS, Goolge Cloud, Azure tương ứng với 3 loại volume là gcePersistentDisk, awsElasticBlockStore, azureDisk.