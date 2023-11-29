### Container hóa là gì ?

- Containerization là một phương pháp triển khai và quản lý ứng dụng, trong đó các ứng dụng và môi trường chạy của chúng được đóng gói cùng nhau trong một container. Container là một đơn vị chứa tất cả các yếu tố cần thiết để chạy một ứng dụng, bao gồm mã nguồn, thư viện, biên dịch, môi trường thực thi, và các cài đặt khác. Điều này tạo ra một môi trường đồng nhất và đảm bảo rằng ứng dụng sẽ chạy một cách đáng tin cậy trên mọi hệ thống có hỗ trợ containerization.

- Containerization giúp giải quyết các vấn đề liên quan đến việc triển khai và chạy ứng dụng trên nhiều môi trường khác nhau bằng cách cung cấp một cách đóng gói độc lập với hệ điều hành và môi trường. Nó cũng tối ưu hóa sử dụng tài nguyên, giảm độ trễ trong quá trình triển khai và mở rộng ứng dụng một cách linh hoạt.

### So sánh ảo hóa và container hóa

| | Containerization | Virtualization |
| -- | -- | -- |
| Khái niệm | Đóng gói ứng dụng và môi trường chạy của nó thành một container. Dùng chung OS với host. | Tạo ra các máy ảo độc lập với phần cứng thực bằng cách mô phỏng phần cứng máy tính. |
| Hiệu suất | Hiệu suất tốt hơn vì nó chia sẻ kernel với hệ thống chủ và không cần lớp giả mạo phần cứng. | Cần một lớp giả mạo phần cứng, do đó có thể có chi phí hiệu suất khiến môi trường ảo hóa chậm hơn so với chạy trực tiếp trên phần cứng. |
| Khởi tạo và triển khai | Nhanh chóng và yêu cầu ít tài nguyên hơn, vì chúng không cần tạo ra một hệ điều hành ảo đầy đủ. | Khởi tạo và triển khai máy ảo yêu cầu thời gian và tài nguyên lớn |
| Đóng gói | Mỗi container chứa một ứng dụng cụ thể và tất cả những gì cần thiết để chạy nó. | Máy ảo là một hệ điều hành đầy đủ và có thể chứa nhiều ứng dụng. |
| Kích thước và tài nguyên | Nhẹ nhàng vì chúng dùng chung kernel và một số thành phần của OS gốc | Tốn nhiều tài nguyên hơn vì mỗi máy ảo có kernel và hệ điều hành của riêng mình |
| Quản lý tài nguyên | Quản lý tài nguyên trên cấp độ container. | Quản lý tài nguyên trên cấp độ máy ảo. |
| Quản lý hệ thống | Đơn giản hóa quá trình triển khai và cập nhật ứng dụng, dễ quản lý hơn. | Đòi hỏi quản lý hệ thống và cập nhật giống như trên máy chủ vật lý. |
| Khả năng cô lập | Sử dụng chung kernel và một số thành phần, chỉ tách biệt về mặt ứng dụng, service. | Cô lập tuyệt đối giữa các ứng dụng và môi trường, sử dụng các phần cứng giả mạo riêng biệt và OS riêng |

![a](https://imgur.com/B0HVijk.png)