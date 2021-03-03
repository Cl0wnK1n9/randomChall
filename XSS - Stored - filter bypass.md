# XSS - Stored - filter bypass ROOTME

link: https://www.root-me.org/en/Challenges/Web-Client/XSS-Stored-filter-bypass

Hết 1 ngày làm việc nhưng chẳng được việc gì =)) thì về solve mấy bài rootme để tự củng cố lại basic cho bản thân và lần này mình chon web client và bài cuối cùng của đống bài về xss store

![img3]()

Về chức năng thì vẫn y hệt những bài trước, chức năng cho phép nhập và `title` và `message`. Ở `title` chỉ cần xuất hiện ký tự `<` là sẽ bị WAF bắt lại và trả về `Hacker detected!`. Ở phần `message` thì nhẹ nhàng hơn tuy nhiên các thẻ `<script>`, `<a>`, `<input>`,.... và kha khá các thẻ khác đều bị filter, sau 1 lúc loai hoay tìm thì vẫn còn thẻ `div` vẫn còn được sử dụng. Nhưng lấy cookie mà không cần tương tác với người dùng bằng thẻ `div` ??? Có lẽ để phần thực thi XSS sau vì để làm được điều đó mình còn cần sử dụng các event như `onmouseover`, `onclick` ... và chỉ cần thêm dấu `=` ngay trước những cụm từ trên đều bị filter .-. thêm nữa ký tự `"` cũng bị filter luôn nên giờ mình sẽ bypass từng cái 1 nào. 

# 1. Bypass onxxxx filter
Điều cần để ý ở đây chính là việc không phải cứ xuất hiện onxxx là sẽ tự động bị filter vì mình vẫn có thể nhập vào giá trị onmouseoverxxx=123 mà vẫn không bị sao, vậy từ đây có thể suy ra nó chỉ filter `onxxx=` và nếu thế thì `onxxxx    =` sẽ ổn thôi và đương nhiên HTML vẫn hiểu như `onxxxx=` không có gì khác cả.

# 2. Bypass `"`
Cái này thì dễ thôi nếu nó cấm thì mình không dùng là được =))) vì việc bạn dùng `onclick="alert(1)"` hay `onclick=alert(1)` thì nó giống nhau miễn là không xuất hiện thêm 1 dấu `=` nữa là được. Còn nếu muốn truyền 1 chuỗi vào vì dù gì mình cũng phải redirect đến webserver của mình để lấy cookie thì sẽ làm như sau: 
- Giả sử window.location="abc" thí đoạn `abc` sẽ sử dụng `String.fromCharCode(46,47,48)`.
- Đối với `window.location=` thì mình sẽ sử dụng hàm `window.location.assign()` và mình thích làm màu nên mình sẽ dùng window["location"]\["assign"]().

# 3. Thực thi javascript mà không cần tương tác của user bằng thẻ div 
Để làm điều này thì chỉ cần bạn chịu khó đọc hay tìm tòi 1 chút sẽ thấy ngay đáp án đó là sử dụng `tabindex=1` kết hợp với `autofocus` và payload nó sẽ dạng như này : 

```<div tabindex=1 onfocus    ={javascript here} autofocus>```

Xong giờ gửi lên và ngồi đợi admin đọc thôi ... sau 10p trôi qua thì vẫn không có gì xảy ra ngoài request từ chính máy của mình còn lại không thêm 1 cái gì ... không hề có request của admin ... thử đi thử lại mấy lần cũng vậy kết quả vẫn thế ... Mình loay hoay 1 lúc không biết mình sai ở đâu hay do bot không hoạt động nữa ??? Vô lý hết sức. 
Sau khoảng tầm 30p không biết tại sao thì mình nghĩ hay do cách xử lý html của mỗi browser một khác chăng và mình chuyển từ chrome sang firefox. Payload của mình không hề hoạt động trên firefox ... để triggle được mã js ở payload trên bắt buộc mình phải bấm vào phím `tab` kể cả khi `tabindex=0`... Một lần nữa đi vào bế tắc tiếp tục thử các thẻ khác và mong chờ 1 điều kỳ diệu xảy ra. Cuối cùng ông trời không phụ lòng trai FA =))) `button` thẻ này không bị filter và nó sử dụng được `autofocus` 

![img3]()


Giờ đi craft lại payload thôi!
