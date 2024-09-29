# Giới thiệu về Inertia

## Ứng dụng JavaScript theo cách monolith hiện đại

Inertia là một phương pháp tiên tiến để xây dựng các ứng dụng web truyền thống được điều khiển bởi server. Chúng ta gọi đây là `monolith hiện đại`.

Inertia mang đến những lợi ích sau:

- Cho phép tạo ra các ứng dụng một trang (single-page apps - SPA) được render hoàn toàn ở phía client.
- Loại bỏ độ phức tạp thường gặp trong các SPA hiện đại.
- Tận dụng các mô hình phía server quen thuộc mà bạn đã sử dụng.

Đặc điểm nổi bật của Inertia:

- Không sử dụng routing phía client.
- Không yêu cầu xây dựng API riêng biệt.
- Cho phép bạn xây dựng controllers và page views một cách quen thuộc.

## Inertia không phải là một framework

Điều quan trọng cần hiểu về Inertia:

- Không phải là một framework mới.
- Không thay thế các framework phía server hoặc client hiện có.
- Được thiết kế để làm việc cùng với các framework sẵn có.

Inertia đóng vai trò như một `chất kết dính` nối kết phía server và client thông qua các bộ chuyển đổi (adapters):

- Bộ chuyển đổi phía client bao gồm: React, Vue và Svelte.
- Bộ chuyển đổi phía server bao gồm: Laravel và Rails.
