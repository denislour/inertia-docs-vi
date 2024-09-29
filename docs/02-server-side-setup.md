# Thiết lập Server-side

## Cài đặt Dependencies

Đầu tiên, cài đặt gem adapter server-side của Inertia và thêm vào Gemfile của ứng dụng bằng lệnh sau:

```bash
bundle add inertia_rails
```

## Rails Generator

Nếu bạn dự định sử dụng Vite làm công cụ build frontend, bạn có thể sử dụng gem `inertia_rails-contrib` để cài đặt và setup Inertia trong ứng dụng Rails. Gem này sẽ tự động kiểm tra xem gem [Vite Rails](https://vite-ruby.netlify.app/guide/rails.html) đã được cài đặt chưa và sẽ cài đặt nó nếu chưa có.

Để cài đặt và setup Inertia trong ứng dụng Rails, chạy lệnh sau trong terminal:

```bash
bundle add inertia_rails-contrib
bin/rails generate inertia:install
```

Lệnh này sẽ thực hiện các bước sau:

- Kiểm tra Vite Rails và cài đặt nếu chưa có
- Hỏi bạn chọn frontend framework ưa thích (React, Vue, hoặc Svelte)
- Hỏi xem bạn có muốn cài đặt Tailwind CSS không
- Cài đặt các dependencies cần thiết
- Setup ứng dụng để làm việc với Inertia
- Tạo controller và view Inertia mẫu (có thể bỏ qua bằng option `--skip-example`)

Sau khi hoàn tất, bạn có thể khởi động Rails server và Vite development server (chúng tôi khuyên bạn nên sử dụng [Overmind](https://github.com/DarthSim/overmind)):

```bash
bin/dev
```

Truy cập `http://localhost:3100/inertia-example` để xem trang Inertia mẫu.

Vậy là xong! Bạn đã setup thành công Inertia trong ứng dụng Rails của mình. Hãy tham khảo hướng dẫn về [tạo pages](/docs/07-pages.md) để biết thêm chi tiết.

## Root Template

Nếu bạn quyết định không sử dụng generator, bạn có thể setup Inertia thủ công trong ứng dụng Rails của mình.

Đầu tiên, tạo root template sẽ được load trong lần truy cập đầu tiên. Template này sẽ được sử dụng để load assets của trang web (CSS và JavaScript), và cũng sẽ chứa một `<div>` root để khởi chạy ứng dụng JavaScript của bạn.

```erb
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csp_meta_tag %>

    <%= inertia_headers %>

    <%# Nếu bạn muốn sử dụng React, thêm `vite_react_refresh_tag` %>
    <%= vite_client_tag %>
    <%= vite_javascript_tag 'application' %>
  </head>

  <body>
    <%= yield %>
  </body>
</html>
```

Template này nên bao gồm assets của bạn, cũng như phương thức `yield` để render trang Inertia. Phương thức `inertia_headers` được sử dụng để thêm các headers Inertia vào response, điều này cần thiết khi [SSR](/docs/02-server-side-rendering.md) được bật.

Adapter của Inertia sẽ sử dụng layout inheritance chuẩn của Rails, với `view/layouts/application.html.erb` làm layout mặc định. Nếu bạn muốn sử dụng layout mặc định khác, bạn có thể thay đổi nó bằng cách sử dụng `InertiaRails.configure`.

```ruby
# config/initializers/inertia_rails.rb
InertiaRails.configure do |config|
  config.layout = 'my_inertia_layout'
end
```

# Tạo Responses

Vậy là bạn đã sẵn sàng ở phía server! Sau khi setup framework [client-side](/docs/03-client-side-setup.md), bạn có thể bắt đầu tạo [pages](/docs/07-pages.md) Inertia và render chúng thông qua [responses](/docs/08-responses.md).

```ruby
class EventsController < ApplicationController
  def show
    event = Event.find(params[:id])

    render inertia: 'Event/Show', props: {
      event: event.as_json(
        only: [:id, :title, :start_date, :description]
      )
    }
  end
end
```
