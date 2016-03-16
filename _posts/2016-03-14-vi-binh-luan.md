---
layout: default
title: Hướng dẫn thêm tính năng comment cho ứng dụng Rails Girls
permalink: /vi/binh-luan
---

# Thêm tính năng Comment cho ứng dụng Rails girl
> Viết bởi Janika Liiv, [@janikaliiv](https://twitter.com/janikaliiv)

Chúng ta sẽ tiến hành bổ sung thêm tính năng comment vào ideas cho ứng dụng `railsgirls` của bạn.

Tài liệu cho việc cài đặt Rails và xây dựng ứng dụng ideas bạn có thể tìm thấy [tại đây](/app)

### *1.* Tạo scaffold cho comment

Tạo một scaffold cho comment, với tên người comment, body của comment (nội dung comment) và tham chiếu (liên kết) đến bảng ideas (`idea_id`)

{% highlight ruby %}
rails g scaffold comment user_name:string body:text idea_id:integer
{% endhighlight %}

Lệnh trên sẽ tạo ra một tập tin migration và báo cho cơ sở dữ liệu của bạn (database) biết về bảng comments mới được tạo ra. Sử dụng lệnh sau để chạy các tệp tin migrations

{% highlight ruby %}
rake db:migrate
{% endhighlight %}

### *2.* Thêm các quan hệ vào các models

Bạn cần phải chắc chắn một điều rằng Rails biết được mỗi quan hệ giữa các đối tượng với nhau (2 đối tượng ở đây là **ideas** và **comments**). Vì một idea có thể có nhiều comments nên chúng ta cần đảm bảo rằng model `idea` biết được điều đó. Mở tập tin `app/models/idea.rb` và sau dòng

{% highlight ruby %}
class Idea < ActiveRecord::Base
{% endhighlight %}

Thêm vào
{% highlight ruby %}
has_many :comments
{% endhighlight %}

Comment cũng cần phải biết rằng nó thuộc về một idea nào đó. Nên hãy mở tập tin `app/models/comment.rb` và sau dòng

{% highlight ruby %}
class Comment < ActiveRecord::Base
{% endhighlight %}

Thêm vào

{% highlight ruby %}
belongs_to :idea
{% endhighlight %}

### *3.* Hiển thị biểu mẫu thêm comment (comment form) và các comment đã tồn tại

Mở tập tin `app/views/ideas/show.html.erb` và sau `image_tag`

{% highlight ruby %}
<%= image_tag(@idea.picture_url, :width => 600) if @idea.picture.present? %>
{% endhighlight %}

Thêm vào

{% highlight ruby %}
<h3>Comments</h3>
<% @comments.each do |comment| %>
  <div>
    <strong><%= comment.user_name %></strong>
    <br />
    <p><%= comment.body %></p>
    <p><%= link_to 'Delete', comment_path(comment), method: :delete, data: { confirm: 'Are you sure?' } %></p>
  </div>
<% end %>
<%= render 'comments/form' %>
{% endhighlight %}

Trong `app/controllers/ideas_controller.rb` thêm vào action `show` các dòng sau

{% highlight ruby %}
@comments = @idea.comments.all
@comment = @idea.comments.build
{% endhighlight %}

Mở tập tin `app/views/comments/_form.html.erb` và sau các dòng sau
{% highlight ruby %}
 <div class="field">
    <%= f.label :body %><br />
    <%= f.text_area :body %>
  </div>
{% endhighlight %}

Thêm vào dòng sau

{% highlight ruby %}
<%= f.hidden_field :idea_id %>
{% endhighlight %}

Và tiếp theo là xóa các dòng sau

{% highlight ruby %}
<div class="field">
  <%= f.label :idea_id %><br>
  <%= f.number_field :idea_id %>
</div>
{% endhighlight %}

Vậy là đã hoàn thành. Bây giờ bạn có thể xem một idea mà bạn đã thêm vào ứng dụng của bạn trước đây và ở đó bạn sẽ thấy một biểu mẫu để thêm vào các comments mới cũng như là xóa các comments cũ.

### Những câu hỏi nâng cao

#### *1.* Dễ

- Di chuyển đến trang `idea/show` sau khi tạo mới một comment
- Thêm trường `like_count` cho một comment (số like cho một comment)
- Thêm trường `picture` cho một comment (trường này sẽ có tác dụng cho phép comment bằng hình ảnh)
- Thêm validation cho các trường `user_name` và `body` trong model `comment`
- Thêm trường `reply_id` cho comment

#### *2.* Bình thường
- Cho phép comment bằng hình ảnh
- Sắp xếp các comment theo thứ tự `like` từ cao đến thấp
- Khi tạo một comment bị lỗi => hiển thị trang idea/show và hiển thị lỗi trên trang đó.

#### *3.* Khó
- Cho phép trả lời các comment
- Thực thi chức năng like cho một comment
