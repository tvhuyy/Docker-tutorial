## Volumes and Persistent Data

- Tìm hiểu cách Docker xử lý và lưu trữ dữ liệu Persistent.

### Sơ lược

- Có 2 loại dữ liệu chính là : Persistent data và non-persistent.
- `Persistent` là dữ liệu cần phải giữ, như là log, record, audit, event,.. `Non-persistent` là dữ liệu mà ta sẽ không cần phải giữ như guest IP,...
- Cả 2 đều quan trọng và Docker có giải pháp cho cả 2 loại.
- Để xử lý dữ liệu `Non-persistent`, mỗi Docker container sẽ có các vùng chứa non-persistent của chính nó. Nó sẽ được tự động tạo ra trên mọi container và được liên kết chặt với vòng đời của container. Vì vậy, mỗi khi container bị xóa thì nó cũng xóa luôn vùng chứa cùng với dữ liệu được lưu trữ trên nó.
- Để xử lý dữ liệu `Persistent`, Container cần chứa dữ liệu trong một `volume`. Các volume là các đối tượng riêng biệt có life-cycle tách riêng với các container. Vì thế nó có thể được tạo và quản lý một cách độc lập, nó sẽ không bị xóa khi container bị xóa.


### Chi tiết


#### Container and non-persistent data

- Container được thiết kế để không thay đổi. Có nghĩa là tốt nhất không nên chỉnh sửa cấu hình của container sau khi deploy. Nếu có gì đó bị lỗi, ta nên tạo một container mới với các cấu hình đã đã sửa và update. Tốt nhất đừng bao giờ log into một container đang chạy và thay đổi cấu hình của nó.
- Tuy nhiên, sẽ có nhiều ứng dụng yêu cầu file system có thể read-write, thậm chí là không chạy trên file system read-only. Để giải quyết vấn đề này thì các container được tạo bởi docker có một thin read-write layer ở trên đỉnh và read-only image layer ở phía dưới.

    ![a](https://imgur.com/vHCMM2k.png)

- Mỗi container layer có thể viết sẽ tồn tại trong filesystem của Docker Host và ta sẽ thấy nó được gọi bằng các tên gọi khác nhau. Nó bao gồm : `local storage`, `ephemeral storage`, `graphdrive storage`. Nó thường nằm trên Docker host ở các vị trí sau :
    - Linux Docker host : `/var/lib/docker/storage-driver>/...`
    - Windows Docker host : `C:\ProgramData\Docker\Windowsfilter\...`

- Layer có thể ghi này là một phần không thể thiếu của nhiều container, nó cho phép thực hiện các hoạt động read-write. Nếu một ứng dụng update file hoặc thêm một file mới, nó sẽ được ghi vào layer này. Tuy nhiên, nó sẽ được gắn với lifecycle của container - sẽ được tạo ra khi tạo container và mất đi khi xóa container. Điều này phù hơp cho các dữ liệu non-persistent.

#### Containers and persistent data

- `Volumes` là cách được khuyên dùng cho persistent data trong container.
    - Volume là một đối tượng độc lập, không được gắn với lifecycle của container.
    - Volume có thể được map với hệ thống lưu trữ chuyên dụng bên ngoài.
    - Volume cho phép nhiều container trên các Docker host khác nhau truy cập và chia sẻ dữ liệu giống nhau.

- Mở rộng hơn, ta có thể tạo một volume, sau đó tạo một container và mount volume vào cho nó. Volume được mount vào thư mục bên trong file system của container, và bất kì thứ gì ghi vào thư mục này sẽ được lưu trữ trên volume. Nếu container bị xóa thì volume và dữ liệu nằm bên trong nó vẫn sẽ tồn tại.
- Ví dụ như hình dưới, Docker volume sẽ ở bên ngoài container như 1 phần riêng biệt. Nó được mount vào thư mục `/code` bên trong file system của container và mọi dữ liệu ghi vào thư mục `/code` đều được lưu trữ trong volume, nó sẽ tồn tại ở đó kể cả khi container bị xóa.
    
    ![a](https://imgur.com/4QdFzmU.png)

- Tất cả các thư mục khác trong container nằm ngoài `/code` đều được ghi vào 'thin writeble container layer' bên trong local storage của Docker host và gắn với life-cycle của container.

#### Tạo và quản lý Docker volumes

- `Volume` là một đối tượng riêng trong Docker, vì thế nó cũng là đối tượng trong API và được tương tác bằng lệnh `docker volume`.
    ```bash
    docker-mng@ubuntu-server:~$ docker volume --help

    Usage:  docker volume COMMAND

    Manage volumes

    Commands:
    create      Create a volume
    inspect     Display detailed information on one or more volumes
    ls          List volumes
    prune       Remove all unused local volumes
    rm          Remove one or more volumes

    Run 'docker volume COMMAND --help' for more information on a command.
    ```

- Mặc định thì Docker sẽ tạo mới volume với build-in local driver. Các volume driver bên thứ ba có thể được sử dụng với `plugins`. Điều này sẽ cung cấp cho Docker các chức năng cao cấp hơn về lưu trữ như `cloud storage service` hoặc các storage tại chỗ như SAN và NAS.

    ![a](https://imgur.com/cCZpYjL.png)

- Tất cả các volume được tạo bằng local driver đều có thư mục riêng trong `/var/lib/docker/volumes` trên Linux và `C:\ProgramData\Docker\volumes` trên Windows. Điều này có nghĩa là nó có thể được nhìn thấy trong file system của Docker Host. Ta có thể truy cập vào nó từ Docker host, tuy nhiên điều này không được recommend cho lắm. 

- Ta đã biết các lệnh để tương tác với volume như `list`,`create`,`inspect`,`delete`,...Tuy nhiên, ta cũng có thể deploy volume thông qua Dockerfiles bằng việc sử dụng chỉ dẫn `VOLUME`. 