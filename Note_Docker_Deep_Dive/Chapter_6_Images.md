## Images

- Tìm hiểu về Docker images, cấu trúc và cách tạo nó

### Sơ lược 

- Một image là package read-only chứa mọi thứ cần thiết để chạy một ứng dụng. Nó bao gồm application code, các application dependency, một tập hợp kiến trúc hệ điều hành tối thiếu và các metadata. Một image có thể được sử dụng để tạo một hoặc nhiều container.

- Nó cũng tương tự như các VM templates. Nếu VM template là VM đã dừng thì Image là container đã dừng. 

- Ta có thể có các container image bằng cách pull chúng từ các registry. Registry phổ biến nhất là Docker Hub, ngoài ra thì cũng có các registry khác. 
- Image có cấu trúc xếp chồng, được tạo thành từ các layer xếp chồng lên nhau. Bên trong nó bao gồm một OS rút gọn và các file, các dependency để chạy ứng dụng. 


### Chi tiết

- Về cơ bản, image giống như container đã dừng. Thực tế, ta có thể build image từ một container đã dừng. Vì vậy, image còn được hiểu là cấu trúc `build-time`, còn container là cấu trúc `run-time`.

    ![a](https://imgur.com/ZhfcnR1.png)

#### Images và containers

- Nhìn vào hình bên trên, ta có thể thấy quan hệ giữa image và container.
- Ta có thể sử dụng lệnh `docker run` để khởi tạo một hoặc nhiều container từ một image. Mỗi khi một container được khởi tạo từ image thì hai cấu trúc này sẽ phụ thuộc vào nhau. Ta không thể xóa image đi nếu như có ít nhất 1 container đang sử dụng image đó.

#### Sự nhỏ gọn của Image

- Mục tiêu của container là để chạy một ứng dựng hoặc một service. Điều này có nghĩa là nó chỉ cần code và các dependency để có thể chạy ứng dụng. Vì vậy nó thực sự nhỏ, do đã loại bỏ các thành phần không cần thiết.
- Ví dụ như Alpine Linux image, nó chỉ có dung lượng là 7MB. Điều này là bởi vì nó không cần bao gồm 6 loại shell khác nhau hay 3 trình quản lý package,... Thậm chí, có một số image còn không có loại shell hay package manager tool nào, vì ứng dụng không cần đến nó.
- Image cũng không cần một kernel, bởi vì nó được share kernel của host. Bình thường là nó chỉ cần các phần quan trọng như file system, các cấu trúc cơ bản,...
- Có ngoại lệ với Windows images, do cách hoạt động của Windows OS nên nó vẫn khá là nặng.