---
layout: default
title: Thiết kế trang web với HTML & CSS
permalink: /vi/thiet-ke
---

# Thiết kế trang web với HTML & CSS

*Được viết bởi Alex Liao, [@alexliao](http://bannka.com/alex). Dịch và chỉnh sửa bởi [@framgia](https://github.com/framgia)*

App của bạn đã có thể chạy tốt, nhưng nhìn nó vẫn khá thô sơ. Chúng ta sẽ đưa một vài thiết kế vào cho bắt mắt.

## *1* Thay đổi layout của toàn bộ ứng dụng
Mở file `app/assets/stylesheets/application.css` và thay dòng

{% highlight css %}
body { padding-top: 100px; }
{% endhighlight %}

bằng dòng

{% highlight css %}
body { padding-top: 60px; }
{% endhighlight %}

Cuối cùng xoá file `app/assets/stylesheets/scaffolds.scss` đi bởi chúng ta không thực sự cần đến thiết kế mặc định của Rails.  
Giờ refresh trang web của bạn tại [http://localhost:3000/ideas](http://localhost:3000/ideas). Bạn vẫn thấy chưa có nhiều thay đổi, tuy nhiên các bước trên là sự chuẩn bị cần thiết cho những bước tiếp theo.

## *2* Điều chỉnh lại menu điều hướng 
Chú ý rằng `idea` là đối tượng quan trọng nhất trong app của bạn, nên chúng ta sẽ tạo một nút `New Idea` trên thanh điều hướng để bạn luôn luôn có thể thấy nó.  
Mở file `app/views/layouts/application.html.erb`, và dưới dòng

{% highlight html %}
<li class="active"><a href="/ideas">Ideas</a></li>
{% endhighlight %}

hãy thêm dòng

{% highlight erb %}
<li ><%= link_to 'New Idea', new_idea_path %></li>
{% endhighlight %}

## *3* Thiết kế trang danh sách Idea
Bây giờ chúng ta sẽ thiết kế lại trang `Ideas` sao cho nhìn thật pro. Để làm được điều đó, chúng ta sẽ thay thế layout dạng `table` sang sử dụng layout với `div`  
**Coach**: Nói một chút về `table` và `div`  

Mở file `app/views/ideas/index.html.erb` trên trình soạn thảo và thay thế tất cả bằng đoạn code dưới đây

{% highlight erb %}
<h1>Listing ideas</h1>

<% @ideas.in_groups_of(3) do |group| %>
  <div class="row">
    <% group.compact.each do |idea| %>
      <div class="col-md-4">
        <%= image_tag idea.picture_url, width: '100%' if idea.picture.present?%>
        <h4><%= link_to idea.name, idea %></h4>
        <%= idea.description %>
      </div>
    <% end %>
  </div>
<% end %>
{% endhighlight %}

**Coach**: Giải thích một chút về ý nghĩa của đoạn code trên, và nói thêm về hệ thống layout 12 grids của Bootstrap

Giờ hãy F5 trang của bạn xem nào! Bạn sẽ thấy trang web đã thay đổi hẳn giao diện. Nhấn vào nút `New Idea`, và tạo thêm nhiều `idea` với những nội dung thật và vài bức ảnh đẹp - trang web của bạn sẽ nhìn còn đẹp hơn nữa khi có nhiều nội dung. Đây cũng là một nguyên tắc trong thiết kế web: Nội dung là cách tốt nhất để trang hoàng cho website của bạn.  

## *4* Thiết kế trang chi tiết Idea
Nhấn vào đề mục của một `idea`, bạn sẽ được điều hướng tới trang chi tiết `Idea`. Bạn có thể thấy nó vẫn giữ giao diện nguyên bản được sinh ra mặc định bởi Rails, bây giờ hãy làm cho nó trở nên đẹp hơn nhé.  
Mở file `app/views/ideas/show.html.erb` trên trình soạn thảo và thay thế toàn bộ bằng đoạn code dưới đây

{% highlight erb %}
<p id="notice"><%= notice %></p>

<div class="row">
  <div class="col-md-9">
    <%= image_tag(@idea.picture_url, width: '100%') if @idea.picture.present? %>
  </div>

  <div class="col-md-3">
    <p><b>Name: </b><%= @idea.name %></p>
    <p><b>Description: </b><%= @idea.description %></p>
    <p>
      <%= link_to 'Edit', edit_idea_path(@idea) %> |
      <%= link_to 'Destroy', @idea, data: { confirm: 'Are you sure?' }, method: :delete %> |
      <%= link_to 'Back', ideas_path %>
    </p>
  </div>
</div>
{% endhighlight %}

**Coach**: Giải thích về ý nghĩa của đoạn code trên. 

# Tiếp theo là gì?

Sau 4 phần trên, các bạn đã có thể hiểu qua công việc thiết kế trang web với HTML & CSS, đồng thời có cái nhìn cơ bản về `Bootstrap`. Chúng ta sẽ đi sâu hơn một chút về Bootstrap ở phần tiếp theo nhé.  

## *A* Thiết kế các nút nhấn 
**Coach**: Đưa ra một vài gợi ý về thiết kế các nút nhấn, biến một đường link trông giống như nút, và giải thích làm thế nào chúng ta có thể với `Bootstrap`

Mở file `app/views/ideas/show.html.erb` và đổi

{% highlight erb %}
<%= link_to 'Edit', edit_idea_path(@idea) %> |
<%= link_to 'Destroy', @idea, data: { confirm: 'Are you sure?' }, method: :delete %> |
<%= link_to 'Back', ideas_path %>
{% endhighlight %}

thành

{% highlight erb %}
<%= link_to 'Edit', edit_idea_path(@idea), class: "btn btn-warning" %> |
<%= link_to 'Destroy', @idea, data: { confirm: 'Are you sure?' }, method: :delete, class: "btn btn-danger" %> |
<%= link_to 'Back', ideas_path, class: "btn btn-info" %>
{% endhighlight %}

OK, tiếp tục F5 trang web của bạn xem nào. Tata...bạn có nhận ra sự thay đổi?

## *B* Thiết kế lại form
**Coach**: Form hiện tại của trang tạo mới `Idea` còn quá thô sơ, chúng ta hãy làm cho nó trở nên đẹp hơn. Có rất nhiều thiết kế đẹp cho form mà chúng ta có thể dễ dàng làm được như:
- Form inline
- Form horizontal
Hãy giải thích kỹ hơn về những kiểu form này. 

Mở file `app/views/ideas/_form.html.erb` và thay đổi dòng

{% highlight erb %}
<%= form_for @idea, html: {multipart: true} do |f| %>
{% endhighlight %}

thành

{% highlight erb %}
<%= form_for @idea, html: {multipart: true, class: "form-horizontal"} do |f| %>
{% endhighlight %}

và thay toàn bộ các đoạn code `<div class="field">` ~ `<div class="actions">` thành

{% highlight erb %}
<div class="form-group">
  <%= f.label :name, class: "col-sm-2 control-label" %>
  <div class="col-sm-10">
    <%= f.text_field :name, class: "form-control", placeholder: "You awesome idea" %>
  </div>
</div>
<div class="form-group">
  <%= f.label :description, class: "col-sm-2 control-label" %>
  <div class="col-sm-10">
    <%= f.text_area :description, class: "form-control", placeholder: "How is your idea?" %>
  </div>
</div>
<div class="form-group">
  <%= f.label :picture, class: "col-sm-2 control-label" %>
  <div class="col-sm-10">
    <%= f.file_field :picture %>
  </div>
</div>
<div class="form-group">
  <div class="col-sm-offset-2 col-sm-10">
    <%= f.submit "Submit your idea", class: "btn btn-success" %>
  </div>
</div>
{% endhighlight %}

Trở lại và F5 trang web của bạn, bạn đã thấy gì?

**Coach**: Giải thích thêm về các css-class `col-sm-*` và `col-sm-offset-*`

## *C* Responsive design
**Coach**: Giải thích về thuật ngữ `responsive design` và Bootstrap đã được tạo ra để đáp ứng phương pháp này như thế nào. Trang web không những cần chạy tốt trên trình duyệt của học viên mà còn phải chạy tốt trên nhiều thiết bị như màn hình lớn, mobile ...

# Ngoài ra, chúng ta có thể phát triển thêm gì nữa?
- Tạo lại form theo ý thích
- Thay đổi thiết kế trang theo ý thích
- Tìm hiểu các tài liệu hướng dẫn khác để học thêm về Rails