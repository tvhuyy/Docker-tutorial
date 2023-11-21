## P2 : Container Runtime

- Một thuật ngữ hơi khó hiểu và cũng có nhiều ý nghĩa với chính cộng đồng Container.
- Ở đây ta sử dụng `runtime` với ý nghĩa như là `sử dụng một ngôn ngữ cụ thể để thực thi chương trình`.
- `Container runtime` chịu trách nhiệm cho tất cả các phần để chạy một container. Để được coi là 1 container runtime thì tính năng cốt lõi phải là chạy container.

- Vấn đề là do sự phổ biến của Docker nên dẫn đến sự hiểu nhầm rằng cách duy nhất để chạy container là thông qua công cụ docker.
- Trước khi docker container runtime ra đời thì đã có các runtime khác, nhưng sự ra đời của docker đã giải quyết trọn gói các vấn đề gặp phải với container :
    - Có 1 định dạng cho container image.
    - Có 1 phương pháp để xây dựng container image (dockerfile, docker build).
    - Có 1 cách quản lý container image (docker image, docker rm)
    - Có 1 cách để quản lý các instance của container (docker ps, docker rm).
    - Có 1 cách để chia sẻ container image (docker pull/push)
    - Có 1 cách để chạy container (docker run).

- Advanced : Docker là runtime high-level và nó chuyển phần việc cho runtime low-level là `runc`.