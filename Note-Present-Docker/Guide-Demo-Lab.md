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

- Dừng container đang chạy bằng lệnh `docker stop <container-name>` và xóa bằng lệnh `docker container rm <container-name>`

#### 3.3 Build Image từ dockerfile

- Với cách này ta cần biết cách viết dockerfile và hiểu cấu trúc của images gồm nhiều layer đã được viết trong phần lý thuyết.
- Về cơ bản, dockerfile là 1 bản hướng dẫn cách xây dựng các layer của images với mỗi `Instruction` là một layer. Các `Instruction` chính thường có trong dockerfile (Chi tiết về các Instruction sẽ được cập nhật sau, phần này chỉ nói khái quát) :
    - `FROM` : dockerfile bắt buộc phải bắt đầu bằng instruction `FROM`. Nó khởi tạo 1 build stage mới và thiết lập base image cho các instruction sau nó.
    - `RUN` : Thực thi lệnh được khai báo trong layer mới.
    - `CMD` : Vẫn là thực thi lệnh nhưng là sau khi run container.
    - `LABEL` : thêm các metadate cho image, dưới dạng các cặp, key-value.
    - `MAINTAINER` : dùng để khai báo tác giả cho Image.
    - `EXPOSE` : khai báo cổng mạng được chỉ định trong lúc container chạy.
    - `ENV` : khai báo các biến môi trường dưới dạng key-value.
    - `COPY` : copy các file hoặc thư mục từ host vào image.
    - `ADD` : tương tự như COPY nhưng có thêm chức năng mở rộng như giải nén hay copy từ URL.
    - `ENTRYPOINT` : cho phép config container như một executable.

- Trong repo đã được clone về ở phần 2, ta truy cập vào thư mục `/apache2`, trong đây đã có sẵn một `dockerfile`. Có thể tham khảo và sử dụng luôn repo này build.

    ```
    FROM ubuntu:latest

    LABEL maintainer="github.com/tvhuyy"

    RUN apt-get update && apt-get install -y apache2

    COPY www/apache-web-81/index.html /var/www/apache-web-81/
    COPY www/apache-web-82/index.html /var/www/apache-web-82/
    COPY default.conf /etc/apache2/sites-available/images.conf

    RUN ln -s /etc/apache2/sites-available/images.conf /etc/apache2/sites-enabled/ && \
        echo listen 81 >> /etc/apache2/ports.conf && \
        echo listen 82 >> /etc/apache2/ports.conf 

    EXPOSE 80 81 82 

    CMD apachectl -D FOREGROUND
    ```

- Các instruction COPY sẽ copy các file html và và file cấu hình vào bên trong image Apache2. Mọi người có thể tùy chỉnh file html ở bên ngoài để web hiển thị thông tin mình muốn.
- Tiến hành build image : `docker build -t my-apache-web:v1 .`
- *Lưu ý* : vì đang build từ cùng thư mục chứa dockerfile nên phần path trong câu lệnh là `.` , nếu ở khác thư mục thì cần chỏ đúng về thư mục chứa dockerfile.

- Kiểm tra image đã được build :
    ```
    docker-manager@Ubuntu-Desk:~/Config-lab-docker/apache2$ docker images
    REPOSITORY       TAG       IMAGE ID       CREATED              SIZE
    my-apache-web    v1        13a900794d72   About a minute ago   240MB
    apache-test      latest    e87cf873a7e2   4 hours ago          294MB
    ubuntu/apache2   latest    8da089b10141   42 hours ago         193MB
    ```

- Run thử container với image này (image này chạy 2 website ở port 81 và 82 bên trong container, cần --publish đúng port):
    ```
    docker run -itd --name apache-web --publish 8081:81 --publish 8082:82 my-apache-web:v1
    ```

- Kiểm tra bằng web browser :

    ![a](https://imgur.com/kdlJtsF.png)

    ![a](https://imgur.com/DlzunPr.png)


### 4. Push Images lên Registry

- Khi ta cần lưu trữ các Image này để có thể mang đi deploy trên các hệ thống khác, ta sẽ cần đến các kho chứa image (registry).
- Phần này sẽ hướng dẫn push lên Docker Hub hoặc đóng gói Images để lưu trữ Private (phù hợp trường hợp triển khai không có mạng).

#### 4.1 Push lên Docker Hub

- Để push lên Docker Hub thì ta cần có tài khoản, đăng ký hoàn toàn miễn phí.
- Sau khi đăng ký xong tài khoản thì ta vào phần `Repositories` và chọn `Create repository`.

    ![a](https://imgur.com/AoWMjgF.png)

- Format của repo là `<name-account>/<name-repository>`. Ví dụ :

    ![a](https://imgur.com/VSgbq40.png)

- Phần bên phải có kèm theo 1 vài hướng dẫn cách tag image name và cách push.
- Ta sẽ cần tag image đang có trên local về thành tag name giống với repo trên Hub để có thể chỉ định image đó được lưu ở đâu.
    ```
    docker-manager@Ubuntu-Desk:~$ docker images
    REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
    my-apache-web    v1        13a900794d72   24 minutes ago   240MB
    apache-test      latest    e87cf873a7e2   4 hours ago      294MB
    ubuntu/apache2   latest    8da089b10141   42 hours ago     193MB
    docker-manager@Ubuntu-Desk:~$ docker tag my-apache-web:v1 hh4huy/apache2:latest
    docker-manager@Ubuntu-Desk:~$ docker images
    REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
    hh4huy/apache2   latest    13a900794d72   26 minutes ago   240MB
    my-apache-web    v1        13a900794d72   26 minutes ago   240MB
    apache-test      latest    e87cf873a7e2   4 hours ago      294MB
    ubuntu/apache2   latest    8da089b10141   42 hours ago     193MB
    ```

- Trên máy lưu image local, ta cần login user docker hub bằng câu lệnh `docker login`, nhập user và password :
    ```bash
    docker-manager@Ubuntu-Desk:~$ docker login
    Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
    You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

    Username: hh4huy
    Password:
    WARNING! Your password will be stored unencrypted in /home/docker-manager/.docker/config.json.
    Configure a credential helper to remove this warning. See
    https://docs.docker.com/engine/reference/commandline/login/#credentials-store

    Login Succeeded

    ```

- Sau khi đã login, ta push image lên :
    ```bash
    docker-manager@Ubuntu-Desk:~$ docker push hh4huy/apache2:latest
    The push refers to repository [docker.io/hh4huy/apache2]
    147919a2b330: Pushed
    772bcc6d75c4: Pushed
    509fa932a129: Pushed
    2aba5c58ee12: Pushed
    76ee02b07b4c: Pushed
    256d88da4185: Mounted from library/ubuntu
    latest: digest: sha256:ce8551085452d20c10c6e4f6494aa454b04cf048d2b857e1bb33a8dcc8524439 size: 1569
    ```

- Bây giờ ta có thể pull nó về từ bất kì máy nào có kết nối internet.
    ```bash
    docker-manager@Ubuntu-Desk:~$ docker search hh4huy/apache2
    NAME             DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
    hh4huy/apache2   Repository include images of Apache2 on Ubun…   0
    ```

#### 4.2 Đóng gói Images lưu trữ Private

- Ta có thể đóng gói 1 images đang có trên máy bằng câu lệnh `docker save` :
    ```
    docker-manager@Ubuntu-Desk:~$ docker save -o hh4huy-apache2.tar hh4huy/apache2
    docker-manager@Ubuntu-Desk:~$ ls
    Config-lab-docker  hh4huy-apache2.tar
    ```

- Sau đó di chuyển đến 1 kho chứa private :
    ```
    tvhuyy@DESKTOP-DCN1VPA:~$ scp docker-manager@192.168.1.22:/home/docker-manager/hh4huy-apache2.tar /home/tvhuyy
    ```

- Rồi chuyển nó sang một server mới từ kho chứa private :
    ```
    tvhuyy@DESKTOP-DCN1VPA:~$ scp hh4huy-apache2.tar tvhuyy@192.168.1.21:/home/tvhuyy/
    ```

- Kiểm tra file trên server mới :
    ```
    tvhuyy@ubuntu-svr2:~$ ls /home/tvhuyy/ | grep hh4huy-apache2.tar
    hh4huy-apache2.tar
    ```

- Giải gói tar thành Image để sử dụng bằng câu lệnh `docker load -i <name-file>` :
    ```
    tvhuyy@ubuntu-svr2:~$ docker load -i hh4huy-apache2.tar
    76ee02b07b4c: Loading layer [==================================================>]  165.1MB/165.1MB
    2aba5c58ee12: Loading layer [==================================================>]  3.584kB/3.584kB
    509fa932a129: Loading layer [==================================================>]  3.584kB/3.584kB
    772bcc6d75c4: Loading layer [==================================================>]  3.584kB/3.584kB
    147919a2b330: Loading layer [==================================================>]  4.096kB/4.096kB
    Loaded image: hh4huy/apache2:latest
    ```

- Kiểm tra Image :
    ```
    tvhuyy@ubuntu-svr2:~$ docker images | grep hh4huy/apache2
    hh4huy/apache2   latest    13a900794d72   47 minutes ago   240MB
    ```