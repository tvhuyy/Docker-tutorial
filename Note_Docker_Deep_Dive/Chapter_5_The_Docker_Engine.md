## The Docker Engine

- Chương này ta sẽ đi tìm hiểu sâu hơn về `Docker Engine`.
- Mặc dù không cần phải hiểu những thứ trong chương này thì ta vẫn có thể dùng Docker, tuy nhiên nếu muốn nắm được cốt lõi của nó thì ta vẫn cần kiến thức sâu của chương này.

### Sơ lược

- `Docker engine` là phần mềm chính để chạy và quản lý các container, hay thường được gọi với tên ngắn gọn là `Docker`. Nếu đã biết về `VMware` thì có thể hiểu nó gần giống như là `ESXi`. 
- Docker engine được thiết kế theo dạng module và cho phép build từ nhiều thành phần. Hầu hết các thành phần dựa theo tiêu chuẩn của OCI (Open Container Initiative).
- Được tạo ra từ nhiều công cụ kết nối với nhau để tạo và chạy container như : API, image builder, high-level runtime, low-level runtime. shims,...
- Tại thời điểm cuốn sách này được viết thì các thành phần chính tạo nên Docker engine là `Docker daemon`, `build system`, `containerd`, `runc` và các plugin khác như `network`, `volume`. Cùng nhau, chúng tạo ra và chạy các container.
    
    ![a](https://imgur.com/vcAH4Iz.png)

### Chi tiết

- Khi Docker ra mắt thì nó có 2 thành phần chính :
    - Docker daemon
    - LXC

- `Docker daemon` là một monolithic binary - chứa các mã nguồn của API, runtime, image builds,...
- `LXC` cung cấp cho daemon quyền truy cập 