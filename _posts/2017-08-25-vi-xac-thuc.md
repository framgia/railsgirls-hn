---
layout: hanoi-201604
title: Thêm tính năng xác thực (authentication) với Devise
permalink: /vi/xac-thuc
---
# Thêm tính năng xác thực người dùng (authentication) với Devise

> Viết bởi Piotr Steininger, [@polishprince](https://twitter.com/polishprince)
> Cập nhật bởi Ernesto Jimenez, [@ernesto_jimenez](https://twitter.com/ernesto_jimenez)

**Hướng dẫn này giả định rằng bạn đã xây dựng một ứng dụng Rails Girl bằng cách làm theo** [hướng dẫn xây dựng ứng dụng](/app)

## *1.* Thêm devise gem

Mở `Gemfile` của bạn và thêm vào dòng sau

{% highlight ruby %}
gem 'devise'
{% endhighlight %}

Và chạy lệnh sau

{% highlight ruby %}
bundle install
{% endhighlight %}

để cài đặt `devise` gem. ** Và nhớ bạn cần phải khởi động lại server **

## *2.* Thiết lập devise cho ứng dụng của bạn

Chạy lệnh sau trên terminal

{% highlight ruby %}
rails g devise:install
{% endhighlight %}

## *3.* Cấu hình Devise

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

## *4*. Thiết lập User model

Chúng ta sẽ sử dụng một generator script để khởi tạo User model

{% highlight ruby %}
 rails g devise user
 rails db:migrate
{% endhighlight %}

**Coach:** Giải thích về user model đã được tạo ra. Các trường (fields) nào được tạo ra và nó là gì?

## *5.* Tạo user đầu tiên

Bây giờ, khi mà bạn đã thiết lập xong tất cả mọi thứ bạn có thể tạo user đầu tiên. Devise đã tạo ra tất cả các đoạn code cũng như routes cần thiết để bạn có thể tạo mới accounts, đăng nhập hay đăng xuất v.v.

Hãy chắc chắn rằng rails server của bạn vẫn đang chạy. Mở [http://localhost:3000/users/sign_up](http://localhost:3000/users/sign_up) (lưu ý đây là địa chỉ server của bạn. Hiện tại hướng dẫn này sử dụng c9.io nên đường dẫn sẽ khác) và tạo tài khoản cho bạn.

## *6.* Thêm vào liên kết đăng kí và đăng nhập

Tất cả những điều chúng ta cần làm bây giờ là thêm liên kết (links) hoặc là thông báo (notice) thích hợp thông báo về tình trạng đăng nhập của người dùng đặt ở góc bên phải của thanh navigation.

Để làm được điều đó, mở tập tin `app/views/layouts/application.html.erb` và thêm vào
{% highlight ruby %}
<p class="navbar-text pull-right">
<% if user_signed_in? %>
  Logged in as <strong><%= current_user.email %></strong>.
  <%= link_to "Edit profile", edit_user_registration_path, :class => 'navbar-link' %> |
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

Cuối cùng, chúng ta sẽ buộc người dùng chuyển hướng đến trang đăng nhập nếu người dùng chưa đăng nhập vào ứng dụng. Mở tập tin `app/controllers/application_controller.rb` và thêm vào dòng sau

{% highlight ruby %}
before_action :authenticate_user!
{% endhighlight %}

ngay sau dòng

{% highlight ruby %}
protect_from_forgery with: :exception
{% endhighlight %}

Mở trình duyệt của bạn và hãy thử đăng nhập, đăng xuất.

**Coach**: Nói về các helpers`user_signed_in?` và `current_user`. Tại sao chúng lại hữu ích?


## Tiếp theo là gì?

- Bổ sung thêm các trường khác cho model User
- Thêm các quan hệ giữa users và ideas
- Hạn chế chỉ cho phép người dùng được xóa những ideas và comments của mình
- Mở rộng thêm về việc dùng roles và permissions (usử dụng một gem phổ biến về authorization như CanCan)

# Những câu hỏi nâng cao

## *A* Dễ
- Thêm trường tên và số điện thoại cho model User
<div class="collapse" id="add_field-example">
Trong cửa sổ dòng lệnh bạn gõ lệnh sau
{% highlight ruby %}
rails generate migration AddUserNameToUsers user_name:string telephone:string
rails db:migrate
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#add_field-example" aria-expanded="false" aria-controls="add_field-example">Code mẫu</button>

- Cấu hình mail server để gửi mail thông báo quên password cho User
<div class="collapse" id="mail-example">
Mở tệp tin <code>config/environments/development.rb</code> và thêm vào những dòng sau trước từ khóa <code>end</code> ở cuối câu
{% highlight ruby %}
config.action_mailer.delivery_method = :smtp
config.action_mailer.smtp_settings = {
address:              'smtp.gmail.com',
port:                 587,
user_name:            '<gmail username>',
password:             '<gmail password>',
authentication:       'plain',
enable_starttls_auto: true  }
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#mail-example" aria-expanded="false" aria-controls="mail-example">Code mẫu</button>

## *B* Bình thường

- Cho phép user sửa đổi thông tin cá nhân của họ (tên, số điện thoại) (bước này gắn liền với bước 1 trong phần dễ)

  Tạo một controller tên là `UsersController` bằng dòng lệnh

{% highlight sh %}
rails generate scaffold_controller Users
{% endhighlight %}

<div class="collapse" id="edit_field-example">
Mở tệp tin <code>config/routes.rb</code> và thêm vào
{% highlight ruby %}
resource :user, only: [:edit, :update]
{% endhighlight %}
Thay toàn bộ nội dung file<code>app/controllers/users_controller.rb</code> với nội dung như sau
{% highlight ruby %}
class UsersController < ApplicationController
  def edit
    @user = current_user
  end

  def update
    @user = current_user
    if @user.update(user_params)
      redirect_to root_path, notice: 'Update Profile was successfully updated.'
    else
      render :edit
    end
  end

  private
  def user_params
    params.require(:user).permit(:user_name, :telephone)
  end
end
{% endhighlight %}
Thay toàn bộ nội dung file <code>app/views/users/edit.html.erb</code> với nội dung sau
{% highlight ruby %}
<h1>Editing Profile</h1>
<%= form_for @user do |f| %>
  <% if @user.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@user.errors.count, "error") %> prohibited this idea from being saved:</h2>

      <ul>
      <% @user.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :user_name %><br>
    <%= f.text_field :user_name %>
  </div>
  <div class="field">
    <%= f.label :telephone %><br>
    <%= f.text_field :telephone %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
{% endhighlight %}
Mở tệp tin <code>app/views/layouts/application.html.erb</code> và thay đổi các dòng

{% highlight erb %}
Logged in as <strong><%= current_user.email %></strong>.
<%= link_to 'Edit profile', edit_user_registration_path, :class => 'navbar-link' %> |
{% endhighlight %}

thành những dòng sau:

{% highlight erb %}
Logged in as <strong><%= current_user.user_name || current_user.email %></strong>.
<%= link_to 'Edit Password', edit_user_registration_path, :class => 'navbar-link' %> |
<%= link_to 'Edit Profile', edit_user_path, :class => 'navbar-link' %> |
{% endhighlight %}

Bây giờ hãy thử khởi động lại server và duyệt tới trang <code>http://localhost:3000/user/edit</code> xem sao?
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#edit_field-example" aria-expanded="false" aria-controls="edit_field-example">Code mẫu</button>

- Thêm quan hệ giữa user và ideas (mỗi 1 user có thể có nhiều ideas)
<div class="collapse" id="user_idea-example">
Trước hết cần thêm vào trường <code>user_id</code> cho bảng <code>ideas</code>
{% highlight ruby %}
rails generate migration AddUserIdToIdeas user_id:integer
rails db:migrate
{% endhighlight %}
Mở tệp tin <code>app/models/user.rb</code> và thêm vào dòng sau
{% highlight ruby %}
has_many :ideas
{% endhighlight %}
Tiếp đó mở tệp tin <code>app/models/idea.rb</code> và thêm vào dòng sau
{% highlight ruby %}
belongs_to :user
{% endhighlight %}
Trong tệp tin <code>app/views/ideas/_form.html.erb</code> bạn thêm vào dòng sau
{% highlight ruby %}
<%= form.hidden_field :user_id, value: current_user.id %>
{% endhighlight %}
Cuối cùng cần cấp quyền truy cập <code>user_id</code> cho <code>ideas_params</code>. Trong tệp tin <code>app/controllers/ideas_controller.rb</code> tìm hàm <code>ideas_params</code> và thay thế dòng code trong hàm bằng dòng dưới đây
{% highlight ruby %}
params.require(:idea).permit(:name, :description, :picture, :user_id)
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#user_idea-example" aria-expanded="false" aria-controls="user_idea-example">Code mẫu</button>

- Thêm quan hệ giữa user và comments (mỗi 1 user có thể có nhiều comments)
<div class="collapse" id="user_comment-example">
Trước hết cần thêm vào trường <code>user_id</code> cho bảng <code>comments</code>
{% highlight ruby %}
rails generate migration AddUserIdToComments user_id:integer
rails db:migrate
{% endhighlight %}
Mở tệp tin <code>app/models/user.rb</code> và thêm vào dòng sau
{% highlight ruby %}
has_many :comments
{% endhighlight %}
Tiếp đó mở tệp tin <code>app/models/comment.rb</code> và thêm vào dòng sau
{% highlight ruby %}
belongs_to :user
{% endhighlight %}
Trong tệp tin <code>app/views/comments/_form.html.erb</code> bạn thêm vào dòng sau
{% highlight ruby %}
<%= form.hidden_field :user_id, value: current_user.id %>
{% endhighlight %}
Cuối cùng cần cấp quyền truy cập <code>user_id</code> cho <code>comments_params</code>. Trong tệp tin <code>app/controllers/comments_controller.rb</code> tìm hàm <code>comments_params</code> và thay thế dòng code trong hàm bằng dòng dưới đây
{% highlight ruby %}
params.require(:comment).permit(:user_name, :body, :idea_id, :picture, :reply_id, :user_id)
{% endhighlight %}
**Lưu ý**: trường <code>:picture</code> và <code>reply_id</code> ở trên là những phần mở rộng cho các câu hỏi trong phần 5, nếu bạn nào bỏ qua phần đó thì ta có thể bỏ 2 trường đó đi.
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#user_comment-example" aria-expanded="false" aria-controls="user_comment-example">Code mẫu</button>

## *C* Khó
- Phân quyền, hoặc vai trò cho mỗi user (ví dụ như admin user có quyền được thêm, xóa hay sửa user, người dùng bình thường chỉ có thể xem được thông tin). Bạn có thể sử dụng những gem phân quyền phổ biến như là `cancancan` hay `the_role`
- Chỉ cho phép user sửa, xóa những ideas và comments do họ tạo ra
- Thêm tính năng đăng nhập thông qua các mạng xã hội (Facebook)
