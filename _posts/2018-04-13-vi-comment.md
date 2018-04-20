---
layout: hanoi-201804
title: Thêm tính năng gửi bình luận
permalink: /vi/comment
---
# Thêm tính năng gửi bình luận


**Hướng dẫn này giả định rằng bạn đã xây dựng một ứng dụng Rails Girl bằng cách làm theo** [hướng dẫn xây dựng ứng dụng](/railsgirls-hn/vi/app)

#1. Tạo comment scaffold
Tạo một scaffold cho comment, với tên người comment, body của comment (nội dung comment) và tham chiếu (liên kết) đến bảng ideas (idea_id)
{% highlight ruby %}
rails g scaffold comment user_name:string body:text idea_id:integer
{% endhighlight %}

**Coach**

Sau khi sử dụng câu lệnh , sẽ tạo ra:
1. Tập tin migration báo cho cơ sở dữ liệu của bạn (database) biết về bảng comments mới được tạo ra.
2. Mội tập tin idea.rb được sinh ra trong thư mục app/model để thao tác với CSDL
3. Một controller để điều khiển viêc thao tác
4. Tạo router
5. Tạo một thư mục trong view có tên ideals

Sử dụng lệnh sau để chạy các tệp tin migrations
{% highlight ruby %}
rails db:migrate
{% endhighlight %}

Khi đó, trong file `schema.rb` sẽ sinh ra bảng ideal trong cơ sở dữ liệu của bạn

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
<%= form_for(@comment) do |f| %>
{% endhighlight %}

thêm vào dòng sau

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

Vậy là đã hoàn thành. Bây giờ bạn có thể xem một `Idea` mà bạn đã thêm vào ứng dụng của bạn trước đây và ở đó bạn sẽ thấy một biểu mẫu để thêm vào các `Comments` mới cũng như là xóa các comments cũ.

### Những câu hỏi nâng cao

#### *1.* Dễ

- Di chuyển đến trang `idea/show` sau khi tạo mới một bình luận (comment)
<div class="collapse" id="button-example">
Mở tệp tin <code>app/controllers/comments_controller.rb</code> trong hàm <code>create</code> thay thế dòng sau
{% highlight ruby %}
format.html { redirect_to @comment, notice: 'Comment was successfully created.' }
{% endhighlight %}
bằng dòng sau
{% highlight ruby %}
format.html { redirect_to idea_path(@comment.idea_id), notice: 'Comment was successfully created.' }
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#button-example" aria-expanded="false" aria-controls="button-example">Code mẫu</button>

- Thêm trường `like_count` cho một bình luận (số like cho một comment)
<div class="collapse" id="like_count-example">
Trong cửa sổ dòng lệnh gõ lệnh sau
{% highlight ruby %}
rails generate migration AddLikeCountToComments like_count:integer
rake db:migrate
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#like_count-example" aria-expanded="false" aria-controls="like_count-example">Code mẫu</button>

- Thêm trường `picture` cho một bình luận (trường này sẽ có tác dụng cho phép bình luận bằng hình ảnh)
<div class="collapse" id="picture-example">
Trong cửa sổ dòng lệnh gõ lệnh sau
{% highlight ruby %}
rails generate migration AddPictureToComments picture:string
rake db:migrate
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#picture-example" aria-expanded="false" aria-controls="picture-example">Code mẫu</button>

- Thêm validation cho các trường `user_name` và `body` trong model `comment`
<div class="collapse" id="validation-example">
Mở tệp tin <code>app/models/comment.rb</code> và thêm vào dòng sau
{% highlight ruby %}
validates_presence_of :user_name, :body
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#validation-example" aria-expanded="false" aria-controls="validation-example">Code mẫu</button>

- Thêm trường `reply_id` cho bình luận
<div class="collapse" id="reply-example">
Trong cửa sổ dòng lệnh gõ
{% highlight ruby %}
rails generate migration AddReplyIdToComments reply_id:integer
rake db:migrate
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#reply-example" aria-expanded="false" aria-controls="reply-example">Code mẫu</button>

#### *2.* Bình thường
- Cho phép bình luận bằng hình ảnh
<div class="collapse" id="image_comment-example">
Mở tệp tin <code>app/models/comment.rb</code> và thêm vào dòng sau
{% highlight ruby %}
mount_uploader :picture, PictureUploader
{% endhighlight %}
Mở tệp tin <code>app/views/comments/_form.html.erb</code> và thêm vào dòng sau
{% highlight ruby %}
<div class="field">
  <%= f.label :picture %><br>
  <%= f.file_field :picture %>
</div>
{% endhighlight %}
Nếu có lỗi xảy ra, hãy thay đổi dòng sau
{% highlight ruby %}
<%= form_for(@comment) do |f| %>
{% endhighlight %}
thành
{% highlight ruby %}
<%= form_for @comment, :html => {:multipart => true} do |f| %>
{% endhighlight %}
Mở tệp tin <code>app/controllers/comments_controller.rb</code> và thay đổi dòng sau
{% highlight ruby %}
def comment_params
   params.require(:comment).permit(:user_name, :body, :idea_id)
end
{% endhighlight %}
thành dòng sau
{% highlight ruby %}
def comment_params
   params.require(:comment).permit(:user_name, :body, :idea_id, :picture)
end
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#image_comment-example" aria-expanded="false" aria-controls="image_comment-example">Code mẫu</button>

- Sắp xếp các bình luận theo thứ tự `like` từ cao đến thấp
<div class="collapse" id="order_like-example">
Mở tệp tin <code>app/controllers/ideas_controller.rb</code> trong hàm <code>show</code> thay thế dòng sau
{% highlight ruby %}
@comments = @idea.comments.all
{% endhighlight %}
bằng
{% highlight ruby %}
@comments = @idea.comments.order(like_count: :desc).all
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#order_like-example" aria-expanded="false" aria-controls="order_like-example">Code mẫu</button>

- Khi tạo một bình luận bị lỗi => hiển thị trang idea/show và hiển thị lỗi trên trang đó.
<div class="collapse" id="error-example">
Mở tệp tin <code>app/controllers/comments_controller.rb</code> trong hàm <code>create</code> bên dưới dòng
{% highlight ruby %}
@comment = Comment.new(comment_params)
{% endhighlight %}
thêm vào dòng sau
{% highlight ruby %}
@idea = Idea.find_by_id @comment.idea_id
@comments = @idea.comments.all
{% endhighlight %}
Thay đổi mã sau
{% highlight ruby %}
respond_to do |format|
  if @comment.save
     format.html { redirect_to @comment, notice: 'Comment was successfully       created.' }
     format.json { render :show, status: :created, location: @comment }
else
    format.html { render :new }
    format.json { render json: @comment.errors, status: :unprocessable_entity }
  end
end
{% endhighlight %}
thành
{% highlight ruby %}
respond_to do |format|
  if @comment.save
    format.html { redirect_to @comment, notice: 'Comment was successfully created.' }
    format.json { render :show, status: :created, location: @comment }
  else
    format.html { render :template => "ideas/show" }
    format.json { render json: @comment.errors, status: :unprocessable_entity }
  end
end
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#error-example" aria-expanded="false" aria-controls="error-example">Code mẫu</button>

#### *3.* Khó
- Cho phép trả lời các comment
- Thực thi chức năng like cho một comment
