---
layout: hanoi-201604
title: Thêm thiết kế cho ứng dụng với HTML & CSS
permalink: /vi/thiet-ke-html-css
---
# Thêm thiết kế cho ứng dụng với HTML & CSS

## *1* Thiết kế header

- Thêm phần code sau vào dưới cùng của file `app/assets/stylesheets/application.css`:

{% highlight css %}
.navbar {
  min-height: 38px;
  background-color: #f55e55;
}
{% endhighlight %}

rồi làm mới lại trang và kiểm tra sự thay đổi. Bạn có thể đổi màu sắc và font của `header` nữa, hãy vào trang [http://color.uisdc.com/](http://color.uisdc.com/) tham khảo xem nhé.  
**Coach**: Nói về thuộc tính `display`, _inline_ và _block_ của phần tử trong trang.

- Sau đó thêm tiếp phần code dưới đây vào dưới cùng file `app/assets/stylesheets/application.css`:

{% highlight css %}
.navbar a.brand { font-size: 18px; }
.navbar a.brand:hover {
  color: #fff;
  background-color: transparent;
  text-decoration: none;
}
{% endhighlight %}

**Coach**: giải thích về 4 trạng thái của một hyper-link.

## *2* Thiết kế table
- Theo như phần trước, để đơn giản chúng ta cũng sẽ sử dụng Twitter [Bootstrap](http://getbootstrap.com/) cho việc trang trí `table`  
Hãy tìm dòng dưới đây trong file `app/views/ideas/index.html.erb`

{% highlight html %}
<table>
{% endhighlight %}
và sửa nó thành
{% highlight html %}
<table class="table">
{% endhighlight %}

- Sửa lại kích thước của ảnh với
{% highlight erb %}
<%= image_tag(idea.picture_url, width: 600) if idea.picture.present? %>
{% endhighlight %}
sau đó bạn có thể tiếp tục sửa thuộc tính `width` và xem sự thay đổi thế nào.  

- Tiếp theo, hãy thêm phần code sau xuống dưới cùng file `app/assets/stylesheets/ideas.css.scss`:
{% highlight css %}
.container a:hover {
  color: #f55e55;
  text-decoration: none;
  background-color: rgba(255, 255, 255, 0);
}
{% endhighlight %}
Bạn có nhận ra thay đổi này không?  
**Coach**: giải thích thêm về thuộc tính `background`

- Ngoài ra, bạn có thể thử vài kiểu nữa với `background-image`. Hãy tham khảo tại [http://subtlepatterns.com/](http://subtlepatterns.com/) nhé.

## *3* Thêm thiết kế cho footer
- Thêm đoạn code sau vào dưới cùng của file `app/assets/stylesheets/application.css`:
{% highlight css %}
footer {
  background-color: #ebebeb;
  padding: 30px 0;
}
{% endhighlight %}
rôi thử thêm vài thứ khác vào phần `footer` trong file `app/views/layouts/application.html.erb` xem thế nào, và điều chỉnh vị trí của nó cho phù hợp nhé.

## *4* Thêm thiết kế cho nút nhấn
- Thêm đoạn code sau vào cuối file `app/assets/stylesheets/ideas.css.scss`:

{% highlight css %}
.container input[type="submit"] {
  height: 30px;
  font-size: 13px;
  background-color: #f55e55;
  border: none;
  color: #fff;
}
{% endhighlight %}

rồi mở đường dẫn [http://localhost:3000/ideas/new](http://localhost:3000/ideas/new) và tìm nút `Create Idea`. Bạn có nhớ trước đó nó trông như thế nào không?

**Coach**: giải thích về thuộc tính `border` và cách sử dụng nó trong CSS, đồng thời hướng dẫn đổi thiết kế của `button` với một số kiểu như bo tròn góc, đổ bóng hoặc đổi màu ...

# Tiếp theo?

## *A* CSS Transform

Chúng ta hãy cùng thử làm cho nút nhấn `Create Idea` nghiêng một góc 45 độ như button dưới đây.  
**Coach**: Nói thêm một chút về sức mạnh của CSS nói chung và CSS3 nói riêng, và gợi ý về cách để làm một nút nhấn nghiêng góc

- Code mẫu dưới đây có thể giúp các bạn làm được nút nhấn như thế này, nhưng thử tìm hiểu tại [W3School](http://www.w3schools.com/cssref/css3_pr_transform.asp) xem sao đã nào.  

<p>&nbsp;</p>
<div class="collapse" id="css-transform-example">
  <ul><li>Thêm đoạn code sau vào cuối file <code>app/assets/stylesheets/ideas.css.scss</code>:</li></ul>
  {% highlight css %}
  .container input[type="submit"] {
    -ms-transform: rotate(45deg);
    -webkit-transform: rotate(45deg);
    transform: rotate(45deg);
  }
  {% endhighlight %}
  Và làm mới trang để xem kết quả
  <p>&nbsp;</p>
</div>
<button class="btn btn-info" style="-ms-transform: rotate(45deg); -webkit-transform: rotate(45deg); transform: rotate(45deg);" type="button" data-toggle="collapse" data-target="#css-transform-example" aria-expanded="false" aria-controls="css-transform-example">
  Code mẫu
</button>

## *B* Thiết kế timeline với Bootstrap

![railsgirls-hn-2016-04-timeline]({{ site.baseurl }}/images/railsgirls-hn-201604.jpg)  
**Coach**: nói lại một chút về giao diện grid của Bootstrap và mô tả một giao diện timeline cho `Comment` của `Idea`  

- Các bạn có thể sửa đổi giao diện thành như trên theo code dưới đây, nhưng trước tiên hãy thử tự làm xem sao.  

<div class="collapse" id="timeline-example">
  <ul><li>Mở file <code>app/views/ideas/show.html.erb</code> và thay đoạn code:</li></ul>
  {% highlight erb %}
  <% @comments.each do |comment| %>
    <div>
      <strong><%= comment.user_name %></strong>
      <br />
      <p><%= comment.body %></p>
      <p><%= link_to 'Delete', comment_path(comment), method: :delete, data: { confirm: 'Are you sure?' } %></p>
    </div>
  <% end %>
  {% endhighlight %}
  bằng
  {% highlight erb %}
  <% @comments.each do |comment| %>
    <div class="row">
      <div class="col-md-3">
        <strong><%= comment.user_name %></strong>
      </div>
      <div class="col-md-8"><%= comment.body %></div>
      <%= link_to 'Delete', comment_path(comment), method: :delete, class: "col-md-1 btn btn-danger", data: { confirm: 'Are you sure?' } %>
    </div>
  <% end %>
  {% endhighlight %}
  và xem giao diện như vậy có dễ đọc hơn không?
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#timeline-example" aria-expanded="false" aria-controls="timeline-example">
  Code mẫu
</button>

- Ngoài ra các bạn có thể áp dụng grid vào form `Comment` để đưa form này vào giao diện timeline của mình nhé

## *C* Các thành phần JavaScript của Bootstrap: collapse 
 
**Coach**: nói thêm về bootstrap và phần hiệu ứng mà sử dụng CSS sẽ khó khăn nhưng với JavaScript thì lại dễ dàng hơn hẳn.  
Để chuẩn bị, chúng ta có thể:

- Mở file `app/views/layouts/application.html.erb` và thêm dòng
{% highlight erb %}
<script src="//maxcdn.bootstrapcdn.com/bootstrap/3.3.6/js/bootstrap.min.js" integrity="sha384-0mSbJDEHialfmuBBQP6A4Qrprq5OVfW37PRR3j5ELqxss1yVqOtnepnHVP9aJ7xS" crossorigin="anonymous"></script>
{% endhighlight %}
vào phía trên dòng
{% highlight erb %}
<%= csrf_meta_tags %>
{% endhighlight %}
- Tham khảo `collapse` cũng như các thành phần khác của Bootstrap Javascript tại [đây](http://getbootstrap.com/2.3.2/javascript.html#collapse)
