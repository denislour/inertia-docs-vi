# Pages

Khi xây dựng ứng dụng với Inertia, mỗi trang thường có một controller/route riêng biệt và một component JavaScript tương ứng. Cách tiếp cận này cho phép bạn chỉ lấy những dữ liệu cần thiết cho trang đó mà không cần phải xây dựng một API riêng.

Hơn nữa, tất cả dữ liệu cần thiết có thể được lấy trước khi trang được render bởi trình duyệt. Điều này loại bỏ việc hiển thị trạng thái "đang tải" khi người dùng truy cập ứng dụng của bạn.

## Tạo Pages

Pages trong Inertia về cơ bản là các component JavaScript. Nếu bạn đã từng làm việc với các component Vue, React, hoặc Svelte, bạn sẽ thấy quen thuộc. Các pages này nhận dữ liệu từ controllers của ứng dụng dưới dạng props.

```js
<script>
  import Layout from '../Layout'

  export let user
</script>

<Layout>
  <svelte:head>
    <title>Welcome</title>
  </svelte:head>
  <H1>Welcome</H1>
  <p>Hello {user.name}, welcome to your first Inertia app!</p>
</Layout>
```

Để render một page, bạn chỉ cần trả về một response Inertia từ controller hoặc route. Ví dụ, giả sử page này được lưu tại `app/frontend/pages/User/Show.(jsx|vue|svelte)` trong một ứng dụng Rails.

```ruby
class UsersController < ApplicationController
  def show
    user = User.find(params[:id])

    render inertia: 'User/Show', props: { user: }
  end
end
```

Để biết thêm chi tiết về cách trả về responses Inertia từ controllers, bạn có thể tham khảo tài liệu về responses.

## Tạo Layouts

Mặc dù không bắt buộc, việc tạo một layout chung cho tất cả các trang thường rất hữu ích cho hầu hết các dự án. Trong ví dụ page ở trên, bạn có thể thấy chúng ta đang bọc nội dung trang trong một component `<Layout>`. Đây là một cách tiếp cận phổ biến để tạo layout.

```js
<script>
  import { inertia } from '@inertiajs/svelte'
</script>

<main>
  <header>
    <a use:inertia href="/">Home</a>
    <a use:inertia href="/about">About</a>
    <a use:inertia href="/contact">Contact</a>
  </header>
  <article>
    <slot />
  </article>
</main>
```

## Persistent Layouts

Mặc dù việc triển khai layouts như các component con của trang là đơn giản, nhưng nó buộc instance layout phải bị hủy và tạo lại giữa các lần truy cập. Điều này có nghĩa là bạn không thể duy trì trạng thái layout khi điều hướng giữa các trang.

```js
<script context="module">
  export { default as layout } from './Layout.svelte'
</script>

<script>
  export let user
</script>

<H1>Welcome</H1>
<p>Hello {user.name}, welcome to your first Inertia app!</p>
```

Ngoài ra, bạn cũng có thể tạo các cấu trúc layout phức tạp hơn bằng cách sử dụng nested layouts.

```js
<script context="module">
  import SiteLayout from './SiteLayout.svelte'
  import NestedLayout from './NestedLayout.svelte'
  export const layout = [SiteLayout, NestedLayout]
</script>

<script>
  export let user
</script>

<H1>Welcome</H1>
<p>Hello {user.name}, welcome to your first Inertia app!</p>
```

## Default Layouts

Khi sử dụng persistent layouts, việc định nghĩa layout trang mặc định trong callback `resolve()` của file JavaScript chính có thể rất thuận tiện.

```js
// frontend/entrypoints/inertia.js
import Layout from "../Layout";

createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.svelte", { eager: true });
    let page = pages[`../pages/${name}.svelte`];
    return { default: page.default, layout: page.layout || Layout };
  },
  // ...
});
```

Cách làm này sẽ tự động đặt layout trang thành `Layout` nếu chưa có layout nào được chỉ định cho trang đó.

Bạn thậm chí có thể tiến xa hơn bằng cách đặt layout trang mặc định dựa trên tên trang, thông tin này có sẵn trong callback `resolve()`. Ví dụ, bạn có thể không muốn áp dụng layout mặc định cho các trang công khai.

```js
// frontend/entrypoints/inertia.js
import Layout from "../Layout";

createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.svelte", { eager: true });
    let page = pages[`../pages/${name}.svelte`];
    return {
      default: page.default,
      layout: name.startsWith("Public/") ? undefined : Layout,
    };
    return page;
  },
  // ...
});
```
