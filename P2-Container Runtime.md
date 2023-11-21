## P2 : Container Runtime

- Một thuật ngữ hơi khó hiểu và cũng có nhiều ý nghĩa với chính cộng đồng Container.
- `Container runtime` là một phần của hệ thống containerization, chịu trách nhiệm cho tất cả các phần để chạy một container, bao gồm cả thực thi và quản lý các container. Để được coi là 1 container runtime thì tính năng cốt lõi phải là chạy container.

### Container runtime thực hiện các nhiệm vụ :

1. Quản lý Container Lifecycle:

    - Container runtime đảm nhận quy trình khởi động, chạy, tạm dừng, và dừng container.

2. Đảm Bảo Độ Cô Lập:

 - Container runtime tạo ra môi trường cô lập để container có thể chạy độc lập với hệ thống chủ và các 
 container khác.

3. Quản Lý Tài Nguyên:

    - Runtime quản lý tài nguyên được cấp phát cho container, như CPU, bộ nhớ, mạng, và lưu trữ.

4. Kết Nối với Kernel của Hệ Điều Hành:

    - Container runtime là giao tiếp trực tiếp với kernel của hệ điều hành để sử dụng các tính năng cô lập của kernel, chẳng hạn như namespaces và cgroups (Namespace cho phép ta ảo hóa tài nguyên hệ thống giống như file system hoặc networking cho mỗi container. Cgroup cung cấp 1 cách để giới hạn lượng tài nguyên như cpu và bộ nhớ mà mỗi container có thể sử dụng).

5. Quản Lý File System:

    - Container runtime đảm bảo rằng các file và thư mục cần thiết cho container đều tồn tại và được quản lý đúng cách.

6. Thực Hiện Đóng Gói và Giải Gói Container:

    - Container runtime giải nén và thực hiện đóng gói các thành phần của container, chẳng hạn như hệ điều hành, ứng dụng, và dependencies.

7. Giao Tiếp với Các Component Khác:

    - Container runtime thường cần giao tiếp với các thành phần khác trong hệ thống containerization, như daemon quản lý container (containerd, Docker daemon) hoặc orchestrator (ví dụ: Kubernetes).


### Một số container runtime phổ biến bao gồm:

- containerd: Là một runtime chủ yếu được sử dụng bởi Docker và các hệ thống containerization khác.

- rkt (rkt, pronounced "rocket"): Là một runtime container phát triển bởi CoreOS.

- cri-o: Là một runtime tối giản được thiết kế đặc biệt để hỗ trợ Kubernetes.


- Vấn đề là do sự phổ biến của Docker nên dẫn đến sự hiểu nhầm rằng cách duy nhất để chạy container là thông qua công cụ docker.

- Nhưng sự ra đời của docker đã giải quyết gần như trọn gói các vấn đề gặp phải với container :

    - Có 1 định dạng cho container image.
    - Có 1 phương pháp để xây dựng container image (dockerfile, docker build).
    - Có 1 cách quản lý container image (docker image, docker rm)
    - Có 1 cách để quản lý các instance của container (docker ps, docker rm).
    - Có 1 cách để chia sẻ container image (docker pull/push)
    - Có 1 cách để chạy container (docker run).

- Advanced : Docker là runtime high-level và nó chuyển phần việc cho runtime low-level là `runc`.