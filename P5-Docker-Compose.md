## Docker-Compose

- Tự động hóa triển khai container trong Docker host với `Docker-Compose`.
- Docker-compose được tối ưu hóa để triển khai các ứng dụng dạng microservice.
- Thay vì ta phải quản lý dịch vụ (container) riêng lẻ, thì có thể sử dụng docker-compose để quản lý nhiều dịch vụ trên các container khác nhau chỉ bằng 1 file cấu hình.

### Docker-Compose file

- Compose sử dụng 2 định dạng file là YAML và JSON để cấu hình multi-service application. Tên mặc định file cấu hình sẽ là `docker-compose.yml` . Ta có thể sử dụng tên khác nhưng khi chạy cần thêm option `-f` để chỉ định file.