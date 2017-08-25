---
layout: hanoi-201604
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

và nhớ khởi động lại server

## *2.* Tạo thumbnails khi ảnh đã được upload

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
<%= image_tag idea.picture_url, width: '100%' if idea.picture.present? %>
{% endhighlight %}

thành

{% highlight ruby %}
<%= image_tag idea.picture_url(:thumb) if idea.picture? %>
{% endhighlight %}

Ngay bây giờ, bạn hãy mở trình duyệt lên để thấy được thumbnail ảnh.

# Những câu hỏi nâng cao

## *A* Dễ
Hiện thị ảnh thumbnail trong trang `app/views/ideas/show.html.erb`
<div class="collapse" id="button-example-1">
Mở tệp tin <code>app/views/ideas/show.html.erb</code> thay thế dòng sau

{% highlight ruby %}
<%= image_tag(@idea.picture_url, width: '100%') if @idea.picture.present? %>
{% endhighlight %}

bằng dòng sau

{% highlight ruby %}
<%= image_tag(@idea.picture_url(:thumb)) if @idea.picture.present? %>
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#button-example-1" aria-expanded="false" aria-controls="button-example-1">Code mẫu</button>

## *B* Bình thường
Định nghĩa thêm những version khác và sử dụng trong trang `app/views/ideas/index.html.erb`. Ví dụ: `version :small_thumb, from_version: :thumb`
<div class="collapse" id="button-example-2">
Mở tệp tin <code>app/uploaders/picture_uploader.rb</code> thêm đoạn ngay bên dưới đoạn mã:

{% highlight ruby %}
version :thumb do
  process :resize_to_fill => [50, 50]
end
{% endhighlight %}

với đoạn mã

{% highlight ruby %}
version :small_thumb, from_version: :thumb do
  process resize_to_fill: [20, 20]
end
{% endhighlight %}

Sửa dòng sau trong <code>app/views/ideas/index.html.erb</code>

{% highlight ruby %}
<%= image_tag idea.picture_url(:thumb) if idea.picture? %>
{% endhighlight %}

bằng dòng

{% highlight ruby %}
<%= image_tag(idea.picture_url(:small_thumb)) if idea.picture.present? %>
{% endhighlight %}
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#button-example-2" aria-expanded="false" aria-controls="button-example-2">Code mẫu</button>

## *C* Khó
Lưu trữ ảnh trên dịch vụ [cloudinary.com](http://cloudinary.com/) sử dụng carrierwave và hiện thị lên trình duyệt trong trang <code>app/views/ideas/index.html.erb</code>.
<div class="collapse" id="button-example-3">
Đăng ký một tài khoản trên website <a href="https://cloudinary.com/users/register/free">cloudinary.com</a>. Sau khi đăng ký, thực hiện đăng nhập, tải file cấu hình <a href="https://cloudinary.com/console/cloudinary.yml">tại đây</a> và lưu vào thư mục <code>config</code>. Chúng ta sẽ có tệp tin cấu hình theo đường dẫn

{% highlight ruby %}
config/cloudinary.yml
{% endhighlight %}

Mở tệp tin <code>Gemfile</code> thêm <code>gem "cloudinary"</code> ngay bên dưới <code>gem "carrierwave"</code>:

{% highlight ruby %}
gem "carrierwave"
gem "cloudinary"
{% endhighlight %}

Sau đó thực hiện cài đặt gem mới bằng lệnh:

{% highlight ruby %}
bundle install
{% endhighlight %}

Mở tệp tin <code>uploaders/picture_uploader.rb</code>, comment các đoạn mã giống như sau:

{% highlight ruby %}
...
# include CarrierWave::MiniMagick
...
# storage :file
...
# def store_dir
#   "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
# end
{% endhighlight %}

Ngay bên dưới dòng:

{% highlight ruby %}
class PictureUploader < CarrierWave::Uploader::Base
{% endhighlight %}

thêm dòng sau

{% highlight ruby %}
include Cloudinary::CarrierWave
{% endhighlight %}

Vậy là xong, bây giờ bạn khởi động lại server và thử tải ảnh mới của một `Idea` và kiểm tra trong trang `/ideas/index` xem nhé.
</div>
<button class="btn btn-info" type="button" data-toggle="collapse" data-target="#button-example-3" aria-expanded="false" aria-controls="button-example-3">Code mẫu</button>
