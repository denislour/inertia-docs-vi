# Thiết lập Client-side

Sau khi bạn đã [cấu hình framework server-side](/docs/02-server-side-setup.md), bạn cần thiết lập framework client-side. Inertia hiện hỗ trợ React, Vue và Svelte.

> [!LƯU Ý]
> Xem [Rails generator](/docs/02-server-side-setup.md#rails-generator) để biết cách nhanh chóng setup Inertia trong ứng dụng Rails của bạn.

## Cài đặt Dependencies

Đầu tiên, cài đặt adapter client-side của Inertia tương ứng với framework bạn chọn.

```shell
npm install @inertiajs/svelte svelte
```

## Khởi tạo ứng dụng Inertia

Tiếp theo, cập nhật file JavaScript chính để khởi động ứng dụng Inertia. Để làm điều này, chúng ta sẽ khởi tạo framework client-side với component Inertia cơ bản.

```js
// frontend/entrypoints/inertia.js
import { createInertiaApp } from "@inertiajs/svelte";

createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.svelte", { eager: true });
    return pages[`../pages/${name}.svelte`];
  },
  setup({ el, App, props }) {
    new App({ target: el, props });
  },
});
```

Callback `setup` nhận tất cả những thứ cần thiết để khởi tạo framework client-side, bao gồm cả component `App` root của Inertia.

# Resolving Components

Callback `resolve` cho Inertia biết cách load một page component. Nó nhận tên page (string) và trả về một module page component. Cách bạn implement callback này phụ thuộc vào bundler (Vite hoặc Webpack) bạn đang sử dụng.

== Svelte

```js
// Vite
// frontend/entrypoints/inertia.js
createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.svelte", { eager: true });
    return pages[`../pages/${name}.svelte`];
  },
  //...
});

// Webpacker/Shakapacker
// javascript/packs/inertia.js
createInertiaApp({
  resolve: (name) => require(`../pages/${name}.svelte`),
  //...
});
```

Mặc định, chúng tôi khuyên bạn nên eager loading các component, điều này sẽ tạo ra một bundle JavaScript duy nhất. Tuy nhiên, nếu bạn muốn lazy-load các component, hãy xem tài liệu về [code splitting](/guide/code-splitting.md) của chúng tôi.

## Định nghĩa Root Element

Mặc định, Inertia giả định rằng root template của ứng dụng của bạn có một root element với `id` là `app`. Nếu root element của ứng dụng của bạn có `id` khác, bạn có thể cung cấp nó bằng cách sử dụng thuộc tính `id`.

```js
createInertiaApp({
  id: "my-app",
  // ...
});
```
