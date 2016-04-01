---
layout: hanoi-201604
title: Hướng dẫn sử dụng gravatar cho ứng dụng Rails Girls
permalink: /vi/gravatar
---

# Thêm Gravatar vào ứng dụng của bạn
> Viết bởi Catherine Jones

Hướng dẫn này giả định rằng bạn đã từng xây dựng một ứng dụng Rails Girls theo [hướng dẫn](https://framgia.github.io/railsgirls-hn/vi/app) và đã sử dụng xác thực người dùng [Devise](https://framgia.github.io/railsgirls-hn/vi/xac-thuc).


__Quan trọng__: Bạn cần phải có một địa chỉ email đã đăng ký với Gravatar. Nếu không có bạn có thể đăng ký tại [gravatar.com](http://en.gravatar.com/).


## *1.* Thêm gem Gravtastic
Mở Gemfile và thêm dòng sau vào ngay bên dưới gem `devise`

{% highlight ruby %}
gem 'gravtastic'
{% endhighlight %}

Trong cửa sổ dòng lệnh thực thi

{% highlight ruby %}
bundle install
{% endhighlight %}

Điều này sẽ cài đặt gem gravtistic. Nhớ khởi động lại server sau khi cài đặt xong.

## *2.* Cài đặt Gravatar trong ứng dụng

Mở `app/models/user.rb`, và thêm những dòng sau vào ngay bên dưới dòng đầu tiên:

{% highlight ruby %}
include Gravtastic
gravtastic
{% endhighlight %}

## *3.* Cấu hình Gravatar
Mở `app/views/layouts/application.html.erb` và trong khoảng giữa:

{% highlight ruby %}
<% if user_signed_in? %>
...
<% else %>

{% endhighlight %}

thêm dòng

{% highlight ruby %}
<%= image_tag current_user.gravatar_url %>
{% endhighlight %}

Ngay bây giờ, bạn hãy mở trình duyệt lên và đăng nhập với địa chỉ email đã đăng ký ở Gravatar. Bạn sẽ nhìn thấy Gravatar của bạn
