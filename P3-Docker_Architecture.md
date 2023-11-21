## P3. Docker Architecture

    ![a](https://imgur.com/YVw4yBJ.png)

- `Docker` sử dụng kiến trúc Client-Server. Client có thể giao tiếp thông qua Docker daemon, nơi xử lý các việc như build, run, phân phối các container. Docker Client và `daemon` có thể chạy trên cùng một hệ thống, hoặc có thể kết nối từ một docker client đến một remote docker daemon. Docker client giao tiếp với daemon sử dụng REST API, thông qua UNIX sockets hoặc network interface.

### Docker daemon

- Docker daemon (dockerd) lắng nghe các Docker API và quản lý các đối tượng trong docker như `images`, `containers`, `networks`, `volumes`.

### Docker client

- `Docker Client` là cách để người dùng tương tác với Docker. Khi sử dụng các lệnh ví dụ như `docker run`, thì client sẽ gửi lệnh đến `dockerd`, nơi chúng sẽ được thực hiện.

