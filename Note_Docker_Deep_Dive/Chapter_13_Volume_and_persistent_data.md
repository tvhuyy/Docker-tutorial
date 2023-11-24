## Volumes and Persistent Data

- Tìm hiểu cách Docker xử lý và lưu trữ dữ liệu Persistent.

### Sơ lược

- Có 2 loại dữ liệu chính là : Persistent data và non-persistent.
- `Persistent` là giữ diệu cần phải giữ, như là log, record, audit, event,.. `Non-persistent` là dữ liệu mà ta sẽ không cần phải giữ như guest IP,...
- Cả 2 đều quan trọng và Docker có giải pháp cho cả 2 loại.
- Để xử lý dữ liệu `Non-persistent`, mỗi Docker container sẽ có các vùng chứa non-persistent của chính nó. Nó sẽ được tự động tạo ra trên mọi container và được liên kết chặt với vòng đời của container. Vì vậy, mỗi khi container bị xóa thì nó cũng xóa luôn vùng chứa cùng với dữ liệu được lưu trữ trên nó.
- Để xử lý dữ liệu `Persistent`, Container cần chứa dữ liệu trong một `volume`. Các volume là các đối tượng riêng biệt có life-cycle tách riêng với các container. Vì thế nó có thể được tạo và quản lý một cách độc lập, nó sẽ không bị xóa khi container bị xóa.


### Chi tiết

- Container được thiết kế để không thay đổi. Có nghĩa là tốt nhất không nên chỉnh sửa cấu hình của container sau khi deploy. Nếu có gì đó bị lỗi, ta nên tạo một container mới với các cấu hình đã đã sửa và update. Tốt nhất đừng bao giờ log into một container đang chạy và thay đổi cấu hình của nó.
- Tuy nhiên, sẽ có nhiều ứng dụng yêu cầu file system có thể read-write, thậm chí là không chạy trên file system read-only. Để giải quyết vấn đề này thì các container được tạo bởi docker có một thin read-write layer ở trên đỉnh và read-only image layer ở phía dưới.

    ![a](https://imgur.com/vHCMM2k.png)

- Mỗi container layer có thể đọc sẽ tồn tại trong filesystem của Docker Host và ta sẽ thấy nó được gọi bằng các tên gọi khác nhau. Nó bao gồm : `local storage`, `ephemeral storage`, `graphdrive storage`. 