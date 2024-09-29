# Thiết lập Client-side

Sau khi bạn đã [cấu hình framework server-side](/02-server-side-setup.md), bạn cần thiết lập framework client-side. Inertia hiện hỗ trợ React, Vue và Svelte.

> [!LƯU Ý]
> Xem [Rails generator](/02-server-side-setup#rails-generator) để biết cách nhanh chóng setup Inertia trong ứng dụng Rails của bạn.

## Cài đặt Dependencies

Đầu tiên, cài đặt adapter client-side của Inertia tương ứng với framework bạn chọn.

:::tabs key:frameworks
== Vue 2

```shell
npm install @inertiajs/vue2 vue@^2
```

== Vue 3

```shell
npm install @inertiajs/vue3 vue
```

== React

```shell
npm install @inertiajs/react react react-dom
```

== Svelte

```shell
npm install @inertiajs/svelte svelte
```

:::

## Khởi tạo ứng dụng Inertia

Tiếp theo, cập nhật file JavaScript chính để khởi động ứng dụng Inertia. Để làm điều này, chúng ta sẽ khởi tạo framework client-side với component Inertia cơ bản.

:::tabs key:frameworks
== Vue 2

```js
// frontend/entrypoints/inertia.js
import Vue from "vue";
import { createInertiaApp } from "@inertiajs/vue2";

createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.vue", { eager: true });
    return pages[`../pages/${name}.vue`];
  },
  setup({ el, App, props, plugin }) {
    Vue.use(plugin);

    new Vue({
      render: (h) => h(App, props),
    }).$mount(el);
  },
});
```

== Vue 3

```js
// frontend/entrypoints/inertia.js
import { createApp, h } from "vue";
import { createInertiaApp } from "@inertiajs/vue3";

createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.vue", { eager: true });
    return pages[`../pages/${name}.vue`];
  },
  setup({ el, App, props, plugin }) {
    createApp({ render: () => h(App, props) })
      .use(plugin)
      .mount(el);
  },
});
```

== React

```js
// frontend/entrypoints/inertia.js
import { createInertiaApp } from "@inertiajs/react";
import { createElement } from "react";
import { createRoot } from "react-dom/client";

createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.jsx", { eager: true });
    return pages[`../pages/${name}.jsx`];
  },
  setup({ el, App, props }) {
    const root = createRoot(el);
    root.render(createElement(App, props));
  },
});
```

== Svelte

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

:::

Callback `setup` nhận tất cả những thứ cần thiết để khởi tạo framework client-side, bao gồm cả component `App` root của Inertia.

# Resolving Components

Callback `resolve` cho Inertia biết cách load một page component. Nó nhận tên page (string) và trả về một module page component. Cách bạn implement callback này phụ thuộc vào bundler (Vite hoặc Webpack) bạn đang sử dụng.

:::tabs key:frameworks
== Vue 2

```js
// Vite
// frontend/entrypoints/inertia.js
createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.vue", { eager: true });
    return pages[`../pages/${name}.vue`];
  },
  // ...
});

// Webpacker/Shakapacker
// javascript/packs/inertia.js
createInertiaApp({
  resolve: (name) => require(`../pages/${name}`),
  // ...
});
```

== Vue 3

```js
// Vite
// frontend/entrypoints/inertia.js
createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.vue", { eager: true });
    return pages[`../pages/${name}.vue`];
  },
  // ...
});

// Webpacker/Shakapacker
// javascript/packs/inertia.js
createInertiaApp({
  resolve: (name) => require(`../pages/${name}`),
  // ...
});
```

== React

```js
// Vite
// frontend/entrypoints/inertia.js
createInertiaApp({
  resolve: (name) => {
    const pages = import.meta.glob("../pages/**/*.jsx", { eager: true });
    return pages[`../pages/${name}.jsx`];
  },
  //...
});

// Webpacker/Shakapacker
// javascript/packs/inertia.js
createInertiaApp({
  resolve: (name) => require(`../pages/${name}`),
  //...
});
```

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

:::

Mặc định, chúng tôi khuyên bạn nên eager loading các component, điều này sẽ tạo ra một bundle JavaScript duy nhất. Tuy nhiên, nếu bạn muốn lazy-load các component, hãy xem tài liệu về [code splitting](/guide/code-splitting.md) của chúng tôi.

## Định nghĩa Root Element

Mặc định, Inertia giả định rằng root template của ứng dụng của bạn có một root element với `id` là `app`. Nếu root element của ứng dụng của bạn có `id` khác, bạn có thể cung cấp nó bằng cách sử dụng thuộc tính `id`.

```js
createInertiaApp({
  id: "my-app",
  // ...
});
```
