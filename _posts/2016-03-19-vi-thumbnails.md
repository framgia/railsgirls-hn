---
layout: default
title: Hướng dẫn tạo thumbnails với Carrierwave cho ứng dụng Rails Girls
permalink: /vi/thumbnails
---

# Tạo thumbnails với Carrierwave
> Viết bởi Miha Filej, [@mfilej](https://twitter.com/mfilej)

__Coach__: Giải thích những quy định cụ thể chiều rộng ảnh trong HTML ở cuối bước 4 và khác như thế nào so với trên server.

## *1.* Cài đặt ImageMagick

- OS X: chạy lệnh trong command `brew install imagemagick`. Nếu bạn không có brew command, bạn có thể [cài đặt Homebrew ở đây](http://mxcl.github.io/homebrew/)
- Windows: tải và chạy [ImageMagick installer](http://www.imagemagick.org/script/binary-releases.php?ImageMagick=vkv0r0at8sjl5qo91788rtuvs3#windows)
- Linux: Trên Ubuntu và Debian, chạy `sudo apt-get install imagemagick`. Với những bản phân phối linux khác, bạn có thể sử dụng trình quản lý riêng thay cho `apt-get`.

__Coach__: ImageMagick là gì và khác với những libraries/gems mà chúng ta sử dụng trước đó như thế nào?

Mở `Gemfile` trong project và thêm:

{% highlight ruby %}
gem 'mini_magick', '3.8.0'
{% endhighlight %}

ngay bên dưới dòng

{% highlight ruby %}
gem 'carrierwave'
{% endhighlight %}

trong Terminal chạy:

{% highlight ruby %}
bundle
{% endhighlight %}

## *2.* Tạo thumbnails khi ảnh được upload lên

Mở `app/uploaders/picture_uploader.rb` và tìm dòng:

{% highlight ruby %}
# include CarrierWave::MiniMagick
{% endhighlight %}

bỏ dấu `#`

__Coach__: Giải thích khái niệm comment trong code

Phía dưới dòng mới thay đổi, thêm:

{% highlight ruby %}
version :thumb do
  process :resize_to_fill => [50, 50]
end
{% endhighlight %}

Những ảnh đã tải lên từ bây giờ đã thay đổi kích thước. Tuy nhiên chúng vẫn chưa được áp dụng với thay đổi trên. Bước tiếp theo chúng ta sẽ áp dụng.

## *3.* Hiện thị thumbnails
Để thấy được những ảnh đã tải lên được thay đổi kích thước, mở `app/views/ideas/index.html.erb`

Thay dòng

{% highlight ruby %}
<td><%= idea.picture %></td>
{% endhighlight %}

thành

{% highlight ruby %}
<td><%= image_tag idea.picture_url(:thumb) if idea.picture? %></td>
{% endhighlight %}

Ngay bây giờ, bạn hãy mở trình duyệt lên để thấy được thumbnail ảnh.

## Những câu hỏi nâng cao

### *1.* Dễ
- Hiện thị ảnh thumbnail trong trang `idea#show`

### *2.* Bình thường
- Định nghĩa thêm những version khác và sử dụng trong trang `idea#index`. Ví dụ: `version :small_thumb, from_version: :thumb`

### *3.* Khó
- Upload ảnh idea bằng carrierwave
