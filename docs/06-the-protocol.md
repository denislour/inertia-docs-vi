# Giao thức

Trang này chứa đặc tả chi tiết về giao thức của Inertia. Hãy đảm bảo đọc trang [cách hoạt động](/05-how-it-works.md) trước để có cái nhìn tổng quan.

## Responses HTML

Request đầu tiên đến một ứng dụng Inertia chỉ là một request trình duyệt thông thường, không có header hoặc dữ liệu Inertia đặc biệt nào. Đối với những request này, server trả về một document HTML đầy đủ.

Response HTML này bao gồm các asset của trang (CSS, JavaScript) cũng như một `<div>` gốc trong phần body của trang. `<div>` gốc đóng vai trò là điểm gắn kết cho ứng dụng phía client, và bao gồm một thuộc tính `data-page` với một [page object] được mã hóa JSON cho trang ban đầu. Inertia sử dụng thông tin này để khởi động framework phía client của bạn và hiển thị component trang ban đầu.

```http
REQUEST
GET: http://example.com/events/80
Accept: text/html, application/xhtml+xml


RESPONSE
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
```

```html
<html>
  <head>
    <title>My app</title>
    <link href="/css/app.css" rel="stylesheet" />
    <script src="/js/app.js" defer></script>
  </head>
  <body>
    <div
      id="app"
      data-page='{"component":"Event","props":{"event":{"id":80,"title":"Birthday party","start_date":"2019-06-02","description":"Come out and celebrate Jonathan&apos;s 36th birthday party!"}},"url":"/events/80","version":"c32b8e4965f418ad16eaebba1d4e960f"}'
    ></div>
  </body>
</html>
```

> Mặc dù response đầu tiên là HTML, Inertia không render server-side các thành phần trang JavaScript.

## Inertia responses

Sau khi ứng dụng Inertia đã được khởi động, tất cả các request tiếp theo đến trang web được thực hiện thông qua XHR với tiêu đề `X-Inertia` được đặt thành `true`. Tiêu đề này cho biết rằng request đang được thực hiện bởi Inertia và không phải là một lần truy cập trang full HTML.

Khi máy chủ phát hiện tiêu đề `X-Inertia`, thay vì trả về một tài liệu full HTML, nó trả về một response JSON với một [page object](/06-the-protocol.md#page-object) được mã hóa.

```json
REQUEST
GET: http://example.com/events/80
Accept: text/html, application/xhtml+xml
X-Requested-With: XMLHttpRequest
X-Inertia: true
X-Inertia-Version: 6b16b94d7c51cbe5b1fa42aac98241d5
RESPONSE
HTTP/1.1 200 OK
Content-Type: application/json
Vary: X-Inertia
X-Inertia: true
{
  "component": "Event",
  "props": {
    "event": {
      "id": 80,
      "title": "Birthday party",
      "start_date": "2019-06-02",
      "description": "Come out and celebrate Jonathan's 36th birthday party!"
    }
  },
  "url": "/events/80",
  "version": "c32b8e4965f418ad16eaebba1d4e960f"
}
```

## Page Object

Inertia chia sẻ dữ liệu giữa server và client thông qua một page object. Object này chứa thông tin cần thiết để render page component, cập nhật trạng thái lịch sử của trình duyệt, và theo dõi phiên bản asset của trang. Page object bao gồm bốn thuộc tính chính:

1. `component`: Tên của component trang JavaScript.
2. `props`: Dữ liệu (props) của trang.
3. `url`: Đường dẫn URL của trang.
4. `version`: Phiên bản asset hiện tại.

Khi truy cập trang đầy đủ, page object được mã hóa JSON vào thuộc tính `data-page` trong thẻ `<div>` gốc. Với các truy cập Inertia, page object được trả về dưới dạng payload JSON.

## Asset Versioning

Một thách thức thường gặp trong các ứng dụng single-page là làm mới assets khi chúng thay đổi. Inertia giải quyết vấn đề này bằng cách theo dõi phiên bản hiện tại của assets trang. Khi asset thay đổi, Inertia sẽ tự động thực hiện truy cập trang đầy đủ thay vì truy cập XHR.

Page object của Inertia bao gồm một định danh `version`. Định danh này được đặt ở phía server và có thể là số, chuỗi, hash file, hoặc bất kỳ giá trị nào đại diện cho "phiên bản" hiện tại của assets trang, miễn là giá trị thay đổi khi assets được cập nhật.

Mỗi khi có request Inertia, phiên bản asset hiện tại được gửi kèm trong header `X-Inertia-Version`. Server so sánh phiên bản này với phiên bản hiện tại, thường được xử lý trong middleware của framework server-side.

Nếu phiên bản trùng khớp, request tiếp tục bình thường. Nếu khác nhau, server trả về response `409 Conflict` kèm URL đích trong header `X-Inertia-Location`. Header này cần thiết vì có thể đã xảy ra chuyển hướng phía server.

Lưu ý, response `409 Conflict` chỉ áp dụng cho request `GET`, không cho `POST/PUT/PATCH/DELETE`. Tuy nhiên, nó vẫn được gửi nếu có chuyển hướng `GET` sau các request này.

Nếu có dữ liệu phiên "flash" khi xảy ra response `409 Conflict`, các adapter framework server-side của Inertia sẽ tự động reflash dữ liệu này.

```http
REQUEST
GET: http://example.com/events/80
Accept: text/html, application/xhtml+xml
X-Requested-With: XMLHttpRequest
X-Inertia: true
X-Inertia-Version: 6b16b94d7c51cbe5b1fa42aac98241d5

RESPONSE
409: Conflict
X-Inertia-Location: http://example.com/events/80
```

## Tải một phần (Partial Reloads)

Khi thực hiện các yêu cầu Inertia, tính năng tải một phần cho phép bạn chỉ yêu cầu một tập hợp con của các props (dữ liệu) từ máy chủ trong các lần truy cập tiếp theo đến cùng một component trang. Đây là một phương pháp tối ưu hóa hiệu suất hữu ích khi có thể chấp nhận một số dữ liệu trang trở nên cũ.

Khi một yêu cầu tải một phần được thực hiện, Inertia sẽ bổ sung hai tiêu đề vào yêu cầu: `X-Inertia-Partial-Data` và `X-Inertia-Partial-Component`.

Tiêu đề `X-Inertia-Partial-Data` chứa danh sách các khóa props (dữ liệu) mong muốn, được phân tách bằng dấu phẩy, cần được trả về.

Tiêu đề `X-Inertia-Partial-Component` chứa tên của component đang được tải một phần. Điều này là cần thiết vì tính năng tải một phần chỉ hoạt động cho các yêu cầu được gửi đến cùng một component trang. Nếu đích cuối cùng khác đi vì bất kỳ lý do nào (ví dụ: người dùng đã đăng xuất và hiện đang ở trang đăng nhập), thì quá trình tải một phần sẽ không diễn ra.

```http
REQUEST
GET: http://example.com/events
Accept: text/html, application/xhtml+xml
X-Requested-With: XMLHttpRequest
X-Inertia: true
X-Inertia-Version: 6b16b94d7c51cbe5b1fa42aac98241d5
X-Inertia-Partial-Data: events
X-Inertia-Partial-Component: Events

RESPONSE
HTTP/1.1 200 OK
Content-Type: application/json

{
  "component": "Events",
  "props": {
    "auth": {...},       // NOT included
    "categories": [...], // NOT included
    "events": [...]      // included
  },
  "url": "/events/80",
  "version": "c32b8e4965f418ad16eaebba1d4e960f"
}

```
