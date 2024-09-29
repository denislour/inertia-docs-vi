# Cách hoạt động

Với Inertia, bạn xây dựng ứng dụng tương tự như cách bạn vẫn làm với framework web server-side quen thuộc. Bạn tận dụng các chức năng có sẵn của framework cho routing, controllers, middleware, xác thực, phân quyền, truy xuất dữ liệu, và nhiều tính năng khác.

Tuy nhiên, Inertia thay thế lớp view trong ứng dụng của bạn. Thay vì sử dụng server-side rendering thông qua các template Ruby, các view được trả về bởi ứng dụng của bạn là các component page JavaScript. Điều này cho phép bạn xây dựng toàn bộ frontend sử dụng React, Vue, hoặc Svelte, trong khi vẫn tận hưởng hiệu suất cao của rails.

Như bạn có thể thấy, việc chỉ tạo frontend bằng JavaScript không tự động mang lại trải nghiệm single-page application. Khi nhấp vào một liên kết, trình duyệt sẽ thực hiện một lần tải trang đầy đủ, khiến framework client-side phải khởi động lại khi tải trang tiếp theo. Đây chính là nơi Inertia tạo ra sự khác biệt.

Về bản chất, Inertia là một thư viện routing phía client. Nó cho phép bạn thực hiện các lần truy cập trang mà không cần tải lại trang đầy đủ. Điều này được thực hiện thông qua component `<Link>`, một wrapper nhẹ bao quanh thẻ liên kết anchor thông thường. Khi bạn nhấp vào một liên kết Inertia, nó sẽ chặn sự kiện click và thực hiện truy cập qua XHR thay vì. Bạn thậm chí có thể thực hiện các lần truy cập này theo chương trình trong JavaScript bằng cách sử dụng `router.visit()`.

Khi Inertia thực hiện một truy cập XHR, server nhận biết đó là một truy cập Inertia và thay vì trả về một response HTML đầy đủ, nó trả về một response JSON chứa tên component page JavaScript và dữ liệu (props). Sau đó, Inertia động thay thế component page trước đó bằng component page mới và cập nhật trạng thái lịch sử của trình duyệt.

**Kết quả cuối cùng là một trải nghiệm single-page mượt mà và hiệu quả. 🎉**

Để hiểu sâu hơn về các chi tiết kỹ thuật và cách Inertia hoạt động bên dưới, bạn có thể tham khảo [trang protocol](/docs/06-the-protocol.md).
