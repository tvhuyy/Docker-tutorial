## P3. Docker Architecture
    
- ![a](https://imgur.com/YVw4yBJ.png)

- `Docker` sử dụng kiến trúc Client-Server. Client có thể giao tiếp thông qua Docker daemon, nơi xử lý các việc như build, run, phân phối các container. Docker Client và `daemon` có thể chạy trên cùng một hệ thống, hoặc có thể kết nối từ một docker client đến một remote docker daemon. Docker client giao tiếp với daemon sử dụng REST API, thông qua UNIX sockets hoặc network interface.

### Docker daemon

- Docker daemon (dockerd) lắng nghe các Docker API và quản lý các đối tượng trong docker như `images`, `containers`, `networks`, `volumes`.

### Docker client

- `Docker Client` là cách để người dùng tương tác với Docker. Khi sử dụng các lệnh ví dụ như `docker run`, thì client sẽ gửi lệnh đến `dockerd`, nơi chúng sẽ được thực hiện.

### Docker Desktop

- Là ứng dụng có thể được cài đặt trên Window, Mac hoặc Linux hỗ trợ giao diện. Nó bao gồm Docker daemon (dockerd), Docker Client (docker), Docker Compose, Docker Content Trust, Kubernetes.

### Docker registries

- Docker registry là kho chứa các `Docker Images`. Docker Hub là một kho chứa public mà tất cả mọi người đều có thể sử dụng và theo mặc định thì Docker sẽ tìm kiếm images trên Docker Hub. Ta cũng có thể sử dụng kho chứa private trên đó. Với bản phải trả phí thì sẽ không bị giới hạn số lượng repo private có thể tạo, ngoài ra còn có chức năng dò quét lỗ hổng images.

### Docker objects

- Khi sử dụng docker, ta cần tạo và sử dụng các images, containers, networks, volumes, plugins và một số đối tượng khác.

#### Images

- Một Images là một template `read-only` với các hướng dẫn để tạo Docker container. Thông thường thì một image sẽ được xây dựng trên nền một image khác với một số các tùy chọn bổ sung. 
- Ta có thể tự build image để sử dụng hoặc sử dụng image có sẵn trên kho chứa.
Để build image thì ta cần tạo `Dockerfile` để mô tả các bước cần thiết để tạo và run image đó. Mỗi trường trong image sẽ tạo một layer, khi ta cần sửa đổi image thì chỉ cần sửa phần layer đó, đây cũng là lí do khiến nó nhẹ hơn rất nhiều so với công nghệ ảo hóa khác.

#### Container

- Là một môi trường tách biệt được run dựa trên image.
- Ta có thể tạo, bắt đầu, dừng, xóa các container bằng cách sử dụng Docker API hoặc Docker CLI.
- Một container có thể được kết nối đến một hoặc nhiều mạng để kết nối đến host và các container khác. Có thể kết nối storage vào cho nó. Và có thể tạo image dựa trên trạng thái hiện tại của container.

- Theo mặc định thì một container được cách ly tương đối tốt với các container khác và với host mà nó đang chạy trên đó. Ta có thể kiểm soát mức độ cô lập về network, storage hoặc các subsystem của container với các container khác hoặc host.

- Một container được xác định bởi image kèm theo các tùy chọn cấu hình khi khởi động. Khi cont bị xóa, mọi thay đổi về trạng thái của container sẽ biến mất.