# Rusty Joomla AND Rootme challenge https://www.root-me.org/en/Challenges/Web-Server/PHP-Unserialize-overflow?action_solution=voir&debut_affiche_solutions=1#pagination_affiche_solutions

Lại là câu chuyện CTF và realworld có giống nhau hay không. Đối với bản thân mình thì 2 cái chẳng khác gì nhau cả có khi realworld còn dễ hơn CTF rất nhiều, lý do thì rõ ràng rồi người tạo ra ứng dụng là lập trình viên - những người không có chuyên môn về ATTT cũng như khả năng lập trình an toàn, trong khi đó đề CTF được ra bởi những người làm ATTT họ hiểu rõ lỗi và nguyên nhân gây ra lỗi đó. Bằng chứng à? đọc thêm phía dưới để thấy nhé lewlew.

# Rusty Joomla: Đôi khi chúng ta đều là những thằng NGU
Trước khi làm challenge của rootme thì việc đầu tiên mình làm sẽ là đọc hiểu tiêu đề mỗi bài đều có tiêu đề rất rõ ràng và nó chính là hint đầu tiên để mindset cách giải của bài đó. Trong trường hợp này là `Unserialize overflow- A Rusty bug`, và sau 1 thời gian google thì mình tìm thấy con bug này `https://blog.hacktivesecurity.com/index.php?controller=post&action=view&id_post=41` và khi đọc thì chính xác nó chẳng khác gì nhau cả, nên cùng vào phân tích 1 chút nhé.
* Serialize problem

  Khi php thực hiện serialize đối với các tham số `protect` trong class thì để phân biệt với `public` và `private` thì các biến `protect` sẽ được thêm padding `\x00*\x00` ở đầu. Vấn đề là đối với 1 số database, chúng ta không thể lưu được ký tự null byte vì nó sẽ gây ra lỗi. Để tránh điều này thì chương trình sẽ thực hiện thay thế các ký tự `\x00*\x00` thành `\0\0\0`. Vấn đề chính là ở đây, tại sao lại thay thế 3 bytes bằng 6 bytes ? Như vậy chẳng phải phá vỡ cấu trúc của dữ liệu được serialize sao ? Kể cả sau đó sẽ thực hiện replace lại tuy nhiên nếu giả sử input là admin\0\0\0 thì chắc chắn có vấn đề. 
  
# CTF Challenge
Quay lại với rootme thì bài này được thiết kế tương tự chỉ bỏ mỗi phần lưu trữ trên database và thay vào đó là lưu trữ trên session.
Theo code thì username và password sẽ được serialize sau khi nhập giá trị và lưu trong cookie. Ý tưởng ở đây sẽ là thêm vào username các chuỗi ký tự `\0\0\0` để thay đổi giá trị của username và dùng giá trị ở password để reconstruct lại thành 1 chuỗi serialize hợp lệ.
* math time
với mỗi 1 lần `\0\0\0` ta sẽ mở rộng được chuỗi ban đầu thêm 3 ký tự bên để ghi đè lên trường password thì cần  xxx lần và password sẽ phải viết lại (cái này mình sẽ để đấy =)) ) cuối cùng sau khi tạo xong username và password hợp lệ mình chỉ cần gửi 1 post request và 1 get  request nữa là có flag.
