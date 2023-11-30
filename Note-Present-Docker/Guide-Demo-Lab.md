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

### 2. Khái quát về Apache2 và Nginx

- Tài liệu này sẽ deploy 2 ứng dụng là Web server `Apache2` và Reverse Proxy `Nginx` để demo Lab trên nền tảng container.
- Mọi người có thể đọc sơ qua về tài liệu triển khai 2 ứng dụng này trên nền server vật lý để hiểu thêm về cách cấu hình và chạy 2 ứng dụng này tại [tvhuyy/Proxy_with_Nginx](https://github.com/tvhuyy/Proxy_with_Nginx_Squid)
- Có thể tự cấu hình các service này trong image hoặc container theo cách hiểu của mình, hoặc nhanh gọn hơn thì mọi người có thể download repository chứa các file cấu hình, file html web, dockerfile, file docker-compose được cấu hình sẵn cho việc lab trong tài liệu demo này bằng câu lệnh :
    ```
    git clone https://github.com/tvhuyy/Config-lab-docker.git
    ```

### 3. Tạo ra và sử dụng Images

- Có 3 cách để tạo ra Images, ta sẽ đi từng cách một :
    - pull từ registry (docker hub).
    - tạo image từ container.
    - build image từ dockerfile.

#### 3.1 Pull Image từ Registry (Dockerfile)

- Cách đầu tiên, là cách đơn giản nhất để chạy 1 service trên container bằng cách pull image từ docker hub.
- Ví dụ, ta cần chạy một web service bằng `Apache2`. Sử dụng lệnh `docker search` trên terminal hoặc tìm trên web [hub.docker.com](https://hub.docker.com/) để tìm các image có sẵn khớp với yêu cầu.
    ```
    docker-manager@Ubuntu-Desk:~$ docker search apache2
    NAME                               DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
    ubuntu/apache2                     Apache, a secure & extensible open-source HT…   65
    rapidfort/apache2-ib               RapidFort optimized, hardened image for Apac…   12
    sismics/apache2                    Apache2 Web Server                              1
    silintl/apache2                                                                    1                    [OK]
    bmltenabled/apache2                                                                0
    sismics/apache2-php                Apache2 + PHP Web Server                        1
    firespring/apache2-php             Based on Ubuntu 16.04. Contains php and a ba…   12                   [OK]
    antage/apache2-php5                Docker image for running Apache 2.x with PHP…   24                   [OK]
    oberonamsterdam/apache24-fpm       Default Oberon apache setup using PHP-FPM, r…   2                    [OK]
    jmferrer/apache2-reverse-proxy     Dockerized apache2 reverse proxy service.       2
    kstaken/apache2                    This a small Apache2 build that can be exten…   3
    antage/apache2-php7                Docker image for running Apache 2.x with PHP…   2                    [OK]
    solution360/apache2-php7-ioncube   Apache2.4 with PHP7 and ioncube.                2                    [OK]
    shajahanak/apache219nov                                                            0
    mhenry07/apache2-utils             Alpine image with apache2-utils (htpasswd, e…   2
    teamrock/apache2                   TeamRock's Apache2                              8                    [OK]
    ascdc/apache2-php56                apache2-php56                                   2                    [OK]
    apache2use/stanbol                 Apache Stanbol 2 Use                            0                    [OK]
    apache2use/kafka                   Apache Kafka enhanced container                 0                    [OK]
    garelp/apache2-galette             Galette installation with php7 and apache2 f…   1
    amarsingh3d/apache2.4-php7.2                                                       0
    apache2use/pig                     Apache Pig enhanced container                   1                    [OK]
    symbiote/apache2                   Ubuntu 16.04 apache2 image, with default vho…   0
    navanieethks/apache2-nav           apache2 test container                          0
    caladreas/apache2-openidc-docker   Naieve docker container configuration for Ap…   1                    [OK]
    ```

    ![a](https://imgur.com/CYkeWpa.png)

- Trong ví dụ này ta sẽ sử dụng image `ubuntu/apache2`.
- Pull Image về :
    ```
    docker-manager@Ubuntu-Desk:~$ docker pull ubuntu/apache2
    Using default tag: latest
    latest: Pulling from ubuntu/apache2
    087fa71db5b5: Pull complete
    2622255df351: Pull complete
    43374381ab64: Pull complete
    Digest: sha256:76ce1dfccfdf4d6d8293b04e68213a40d7dd9435ca747f7a9684f6072890622a
    Status: Downloaded newer image for ubuntu/apache2:latest
    docker.io/ubuntu/apache2:latest
    ```

- Run container từ image này với lệnh `docker run` kèm theo 1 số option cơ bản : `-itd` gộp 3 option là `i` để giữ STDIN luôn mở dù không attached vào container, `t` để chỉ định pseudo-TTY, `d` để chạy container dưới background, `--name` để đặt tên container, `--publish` để publist port của container ra host và trường cuối cùng là image ta chỉ định.
    ```
    docker run -itd --name apache-test --publish 8080:80 ubuntu/apache2
    ```

- Sử dụng lệnh `docker ps` để kiểm tra trạng thái container vừa run :
    ```
    docker-manager@Ubuntu-Desk:~$ docker ps
    CONTAINER ID   IMAGE            COMMAND                CREATED         STATUS         PORTS                                   NAMES
    5555e251a04f   ubuntu/apache2   "apache2-foreground"   3 seconds ago   Up 2 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   apache-test
    ```

- Mặc định khi chưa cấu hình thì service apache đã có sẵn 1 web default, sử dụng lệnh `curl` tại terminal hoặc dùng web browser để truy cập :
    
    ![a](https://imgur.com/CZZFtfo.png)


#### 3.2 Tạo Image từ container :

- Ta sẽ sử dụng luôn container đang chạy ở phần trên.
- Truy cập vào container để sửa file html default của nó :
    ```
    docker exec -it apache-test /bin/bash
    ```

- Mặc định image sẽ nhỏ gọn và không nên sửa gì nên nó không có sẵn trình editor nào, ta cần cài thêm trình editor để có thể sửa file :
    ```
    apt update
    apt install vim
    ```

- Tiến hành sửa file html :
    ```
    vim /var/www/html/index.html
    ```

- Xóa toàn bộ text trong file và thay thế bằng dòng html như trong docs ở phần 2 và lưu lại :
    ```
    <html><body><h1>Virtual Host on Apache Web Server</h1></body></html>
    ```

- Sử dụng tổ hợp phím `Ctrl p + q` để thoát khỏi container mà vẫn giữ nó chạy ngầm.
- Quay lại Web browser và load lại trang web :
    
    ![a](https://imgur.com/WkQM1A0.png)

- Bây giờ ta sẽ lưu trạng thái hiện tại của container thành 1 image để có thể mang đi sử dụng :
    ```
    docker-manager@Ubuntu-Desk:~$ docker commit apache-test
    sha256:e87cf873a7e2bb070916c3ef859a8e7a69be99fbd16dc057307375a04768f8cc
    ```

- Sau khi commit thì image chỉ là 1 dãy mã hash :
    ```
    docker-manager@Ubuntu-Desk:~$ docker image ls
    REPOSITORY       TAG       IMAGE ID       CREATED              SIZE
    <none>           <none>    e87cf873a7e2   About a minute ago   294MB
    ubuntu/apache2   latest    8da089b10141   38 hours ago         193MB
    ```

- Sử dụng `docker tag` để thêm tên cho image, dễ dàng cho việc quản lý :
    ```
    docker-manager@Ubuntu-Desk:~$ docker tag e87c apache-test
    docker-manager@Ubuntu-Desk:~$ docker image ls
    REPOSITORY       TAG       IMAGE ID       CREATED         SIZE
    apache-test      latest    e87cf873a7e2   3 minutes ago   294MB
    ubuntu/apache2   latest    8da089b10141   38 hours ago    193MB
    ```

#### 3.3 Build Image từ dockerfile

