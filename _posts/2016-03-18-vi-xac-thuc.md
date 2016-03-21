---
layout: default
title: Thêm tính năng xác thực (authentication) với Devise
permalink: /vi/xac-thuc
---
# Thêm tính năng xác thực người dùng (authentication) với Devise

> Viết bởi Piotr Steininger, [@polishprince](https://twitter.com/polishprince)
> Cập nhật bởi Ernesto Jimenez, [@ernesto_jimenez](https://twitter.com/ernesto_jimenez)

**Hướng dẫn này giả định rằng bạn đã xây dựng một ứng dụng Rails Girl bằng cách làm theo** [hướng dẫn xây dựng ứng dụng](/app)

### *1.* Thêm devise gem

Mở `Gemfile` của bạn và thêm vào dòng sau

{% highlight ruby %}
gem 'devise'
{% endhighlight %}

Và chạy lệnh sau

{% highlight ruby %}
bundle install
{% endhighlight %}

để cài đặt `devise` gem. ** Và nhớ bạn cần phải khởi động lại server **

### *2.* Thiết lập devise cho ứng dụng của bạn

Chạy lện sau trên terminal.

{% highlight ruby %}
rails g devise:install
{% endhighlight %}

### *3.* Cấu hình Devise

Hãy chắc chắn rằng bạn đã định nghĩa một `default_url_options` trong các tập tin môi trường của bạn. Mở tập tin `config/environments/development.rb` và thêm vào dòng sau

{% highlight ruby %}
 config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
{% endhighlight %}

trước từ khóa `end` ở cuối tập tin đó.

Mở tập tin `app/views/layouts/application.html.erb` và thêm vào

{% highlight ruby %}
<% if notice %>
  <p class="alert alert-success"><%= notice %></p>
<% end %>
<% if alert %>
  <p class="alert alert-danger"><%= alert %></p>
<% end %>
{% endhighlight %}

ngay phía trên

{% highlight ruby %}
 <%= yield %>
{% endhighlight %}

Mở tập tin `app/views/ideas/show.html.erb` và xóa dòng có nội dung như sau

{% highlight ruby %}
<p id="notice"><%= notice %></p>
{% endhighlight %}

Thực hiện tương tự với tập tin `app/views/comments/show.html.erb`.  Những dòng thông báo này là không cần thiết vì chúng ta đã đưa chúng vào tập tin `app/views/layouts/application.html.erb`.

### *4.* Thiết lập User model

Chúng ta sẽ sử dụng một generator script để khởi tạo User model

{% highlight ruby %}
 rails g devise user
 rake db:migrate
{% endhighlight %}

**Huấn luyện viên:** Giải thích về user model đã được tạo ra. Các trường (fields) nào được tạo ra và nó là gì?

### *5.* Tạo user đầu tiên

Bây giờ, khi mà bạn đã thiết lập xong tất cả mọi thứ bạn có thể tạo user đầu tiên. Devise đã tạo ra tất cả các đoạn code cũng như routes cần thiết để bạn có thể tạo mới accounts, đăng nhập hay đăng xuất v.v.

Hãy chắc chắn rằng rails server của bạn vẫn đang chạy. Mở [http://localhost:3000/users/sign_up](http://localhost:3000/users/sign_up) (lưu ý đây là địa chỉ server của bạn. Hiện tại hướng dẫn này sử dụng c9.io nên đường dẫn sẽ khác) và tạo tài khoản cho bạn.

### *6.* Thêm vào liên kết đăng kí và đăng nhập

Tất cả những điều chúng ta cần làm bây giờ là thêm liên kết (links) hoặc là notice thích hợp thông báo về tình trạng đăng nhập của người dùng đặt ở góc bên phải của thanh navigation.

Để làm được điều đó, mở tập tin `app/views/layouts/application.html.erb` và thêm vào
{% highlight ruby %}
<p class="navbar-text pull-right">
<% if user_signed_in? %>
  Logged in as <strong><%= current_user.email %></strong>.
  <%= link_to 'Edit profile', edit_user_registration_path, :class => 'navbar-link' %> |
  <%= link_to "Logout", destroy_user_session_path, method: :delete, :class => 'navbar-link'  %>
<% else %>
  <%= link_to "Sign up", new_user_registration_path, :class => 'navbar-link'  %> |
  <%= link_to "Login", new_user_session_path, :class => 'navbar-link'  %>
<% end %>
{% endhighlight %}

vào ngay dưới những dòng sau

{% highlight ruby %}
<ul class="nav">
  <li class="active"><a href="/ideas">Ideas</a></li>
</ul>
{% endhighlight %}

Cuối cùng, chúng ta sẽ buộc người dùng chuyển hướng đến trang đăng nhập nếu người dùng chưa đăng nhập và ứng dụng. Mở tập tin `app/controllers/application_controller.rb` và thêm vào dòng sau

{% highlight ruby %}
before_action :authenticate_user!
{% endhighlight %}

ngay sau dòng

{% highlight ruby %}
protect_from_forgery with: :exception
{% endhighlight %}

Mở trình duyệt của bạn và hãy thử đăng nhập, đăng xuất.
** Huấn luyện viên: ** Nói về các helpers`user_signed_in?` và `current_user`. Tại sao chúng lại hữu ích?

## Những câu hỏi nâng cao

### *1.* Dễ
- Thêm trường tên và số điện thoại cho model User
- Cấu hình mail server để gửi mail thông báo quên password cho User

### *2.* Bình thường
- Cho phép user sửa đổi thông tin cá nhân của họ (tên, số điện thoại) (bước này gắn liền với bước 1 trong phần dễ)
- Thêm quan hệ giữa user và ideas (mỗi 1 user có thể có nhiều ideas)
- Thêm quan hệ giữa user và comments (mỗi 1 user có thể có nhiều comments)

### *3.* Khó
- Phân quyền, hoặc vai trò cho mỗi user (ví dụ như admin user có quyền được thêm, xóa hay sửa user, người dùng bình thường chỉ có thể xem được thông tin). Bạn có thể sử dụng những gem phân quyền phổ biến như là `cancancan` hay `the_role`
- Chỉ cho phép user sửa, xóa những ideas và comments do họ tạo ra
- Thêm tính năng đăng nhập thông qua các mạng xã hội (Facebook)