## Tài liệu hướng dẫn Lab Docker

- Tài liệu này sẽ hướng dẫn 
    - cài docker engine
    - viết dockerfile, sử dụng dockerfile
    - pull,push image với docker hub registry
    - Run container cơ bản với `docker run`
    - viết file docker-compose với yaml
    - Ngoài ra để phục vụ dựng ứng dụng để lab trên nền container, tài liệu sẽ hướng dẫn sơ qua về Apache 2 và Nginx.

### System requirements

- Docker có thể được cài đặt trên hầu như tất cả các hệ điều hành, như windows, mac, các bản phân phối linux, hay trên các OS nhỏ hơn như Raspberry Pi. Mỗi OS sẽ có yêu cầu về phần cứng riêng.
- Đối với Linux. Docker cung cấp các packet `.deb` và `.rpm` để có thể cài đặt trên các Linux distros và architectures :

    ![a](https://imgur.com/Aw93sI3.png)

- Host OS được dùng trong tài liệu này là `Ubuntu`, nên dùng bản 64-bit thuộc một trong các phiên bản sau :
    ```
    Ubuntu Mantic 23.10
    Ubuntu Lunar 23.04
    Ubuntu Jammy 22.04 (LTS)
    Ubuntu Focal 20.04 (LTS)
    ```

- Yêu cầu phần cứng cho OS Linux Ubuntu : 
    - tối thiểu 512MB (recommend 2GB trở lên)
    - CPU tối thiểu 1 core (nâng lên tùy vào ứng dụng cần chạy)
    - Disk (tùy thuộc vào số lượng container cần chạy)

- Ngoài ra sẽ cần tài khoản trên `Docker hub` nếu muốn tự tạo images và push lên Docker Hub (Nếu chỉ Pull các image có sẵn từ docker hub thì không cần).


### 1. Cài đặt Docker Engine

- Đầu tiên, đối với các máy mới và cài Docker Engine lần đầu. Cần setup `Docker repository` để có thể cài đặt và cập nhật các package Docker từ repository của chính Docker cung cấp.

    ```
    # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl gnupg
    sudo install -m 0755 -d /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    sudo chmod a+r /etc/apt/keyrings/docker.gpg

    # Add the repository to Apt sources:
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    ```

- Cài đặt Docker package :

    ```bash
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```

- Kiểm tra Docker Engine đã được cài thành công :
    ```
    sudo systemctl status docker
    ```

- Thử run container (Câu lệnh bên dưới sẽ download một image test và run nó trong 1 container).
    ```
    docker run hello-world
    ```

- **Lưu ý** : Theo mặc định, lệnh `docker` chỉ có thể được thực thi với user `root` hoặc bởi các user nằm trong group `docker`, Group này sẽ được tạo tự động khi cài đặt docker. Nếu ta muốn chạy docker mà không dùng `sudo` hoặc `user root` thì ta cần add username vào group `docker` 
    ```
    sudo usermod -aG docker username
    ```

### 2.