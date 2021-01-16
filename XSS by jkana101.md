# link : https://xss-chall.herokuapp.com/
Tại mình lười chụp ảnh nên =))) mọi người cố gắng làm trước rồi tự tưởng tượng nhé =))); thankyou and love u 0x0bb8

Đầu tiên mô tả qua thì tác giả cho mình một form để đưa payload vào url nó sẽ như này -> `https://xss-chall.herokuapp.com/?XSS=sudo&submit=Submit`

Xem trong source HTML ta nhận ra đoạn javascript này sẽ hiển thị nội dung của input vào thẻ có id là `demo`
```<script type="text/javascript">var x = 'sudo';document.getElementById("demo").innerHTML = x;</script>```
Và input của ta khi được sẽ dùng làm giá trị cho phép gán trong thẻ `script`. Ý tưởng ban đầu của mình sẽ bắt đầu từ việc đơn giản nhất, đó là break khỏi phép gán và gọi alert(1).Payload cho việc này sẽ là `'; alert(1);var y='`. Tuy nhiên thì đời không như mơ =((( filter. Sau một hồi mò mẫm thì mình nhận ra `;` bị cấm, bạn không thể gọi hàm kiểu như alert(1) hay eval('1+1') tuy nhiên những thứ như kiểu `[]()`  thì vẫn sẽ hoạt động bình thường. Vậy có 2 vấn đề: ```1 Làm sao để thực thi hàm mà không cần tách câu lệnh có sẵn? 
2 Làm sao để gọi hàm alert ?```

# 1 Làm sao để thực thi hàm mà không cần tách câu lệnh có sẵn?
```var a = 'a' * alert(1) * 'a';```
Javascript cho phép điều này xảy ra có lẽ là do hàm alert thực chất vẫn có giá trị trả về. Không tin thử mà xem 

# 2 Làm sao để gọi hàm alert? 
Thực ra chúng ta có nhiều hơn 1 cách để sử dụng hàm alert mọi người có thể tham khảo tại đây : `https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection`

payload : `ahihi không cho đâu tự làm đi =))) lewlew :p`

