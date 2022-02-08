# Chapter 9: Redirection

```
Parameter                   Details
internal file descriptor    An integer.
direction                   One of >, < or <>
external file descriptor or path & followed by an integer for file descriptor or a path.
```



### Mục lục
[Section 9.1: Redirecting standard output](#section91)<br>
[Section 9.2: Append vs Truncate](#section92)<br>
[Section 9.3: Redirecting both STDOUT and STDERR](#section93)<br>
[Section 9.4: Using named pipes](#section94)<br>
[Section 9.5: Redirection to network addresses](#section95)<br>
[Section 9.6: Print error messages to stderr](#section96)<br>
[Section 9.7: Redirecting multiple commands to the same file](#section97)<br>
[Section 9.8: Redirecting STDIN](#section98)<br>


<a name="section91"></a>
## Section 9.1: Redirecting standard output

> redirect the standard output (aka STDOUT) of the current command into a file or another descriptor.

`>` redirect - chuyển hướng tới standard output (aka STDOUT) của command hiện tại thành một file hoặc descriptor khác.

Ví dụ này ghi kết quả đầu ra của lệnh `ls` vào tệp tin `file.txt`

```
ls >file.txt
> file.txt ls
```

```
[root@vt ~]# ls >file.txt
[root@vt ~]# cat file.txt
anaconda-ks.cfg
bash.sh
duydm
file.txt
greetings.txt
hello.txt.gz
howdy.txt.gz
log.txt
[root@vt ~]#
```

```
[root@vt ~]# > file.txt ls
[root@vt ~]# cat file.txt
anaconda-ks.cfg
bash.sh
duydm
file.txt
greetings.txt
hello.txt.gz
howdy.txt.gz
log.txt
[root@vt ~]#
```

Tệp đích được tạo nếu nó không tồn tại, nếu không tệp này sẽ bị truncated.

Mặc định redirection có đầu ra standard output hoặc 1 khi không có giá trị nào được chỉ định.

```
[root@vt ~]# cat file.txt
anaconda-ks.cfg
bash.sh
duydm
file.txt
greetings.txt
hello.txt.gz
howdy.txt.gz
log.txt
[root@vt ~]#
```

Note: the redirection is initialized by the executed shell and not by the executed command, therefore it is done before the command execution.

Lưu ý: redirection được khởi tạo bởi executed shell chứ không phải bởi executed command, do đó nó được thực hiện trước khi command execution.

<a name="section92"></a>
### Section 9.2: Append vs Truncate

**Truncate >**

```
1. Create specified file if it does not exist.
2. Truncate (remove file's content)
3. Write to file
```

```
[root@vt ~]# echo "first line" > /tmp/lines
[root@vt ~]# echo "second line" > /tmp/lines
[root@vt ~]#  cat /tmp/lines
second line
[root@vt ~]#
```

**Append >>**

```
1. Create specified file if it does not exist.
2. Append file (writing at end of file).
```

```
# Overwrite existing file
[root@vt ~]# echo "first line" > /tmp/lines
# Append a second line
[root@vt ~]# echo "second line" >> /tmp/lines
[root@vt ~]# cat /tmp/lines
first line
second line
[root@vt ~]#
```

<a name="section93"></a>
### Section 9.3: Redirecting both STDOUT and STDERR

File descriptors like 0 and 1 are pointers. We change what file descriptors point to with redirection. >/dev/null means 1 points to /dev/null.

File descriptors như 0 và 1 là các pointers. Chúng tôi thay đổi những gì mà file descriptors point đến bằng redirection. `>/dev/null` có nghĩa là 1 point đến `/dev/null`.

First we point 1 (STDOUT) to `/dev/null` then point 2 (STDERR) to whatever 1 points to.

```
# STDERR is redirect to STDOUT: redirected to /dev/null,
# effectually redirecting both STDERR and STDOUT to /dev/null
echo 'hello' > /dev/null 2>&1
Version ≥ 4.0
```

Điều này có thể được rút ngắn hơn nữa thành sau:

```
echo 'hello' &> /dev/null
```

Tuy nhiên, form này có thể không được mong muốn trong quá trình sản xuất nếu mối quan tâm về khả năng tương thích của shell vì nó xung đột với POSIX, gây ra sự mơ hồ về phân tích cú pháp và các shell không có tính năng này sẽ hiểu sai về nó: 

```
# Actual code
echo 'hello' &> /dev/null
echo 'hello' &> /dev/null 'goodbye'
# Desired behavior
echo 'hello' > /dev/null 2>&1
echo 'hello' 'goodbye' > /dev/null 2>&1
# Actual behavior
echo 'hello' &
echo 'hello' & goodbye > /dev/null
```

Mọi thứ trong linux đều là file, ngay cả các standard input, output và error cũng đều là file. Mỗi file đều có một cái định danh gì đó để có thể cầm nắm hay thao tác được đó là các file descriptor. Một file descriptor được diễn đạt như một số nguyên dương, các chuẩn input, output và error cũng không phải ngoại lệ, các file desciptor của chúng có giá trị lần lượt:

```
standard input : 0
standard ouput : 1
standard error:  2
```

Còn `/dev/null` cũng là một file đặc biệt trong `linux/unix` thường được dùng để chứa các dữ liệu rác từ các `input stream` khi chúng ta không muốn xử lý hay muốn hiển thị nó, nói dễ hiểu hơn `/dev/null` giống như một hố có thể chứa tất cả các dữ liệu được redirect tới nó.

Các toán tử `>` là các toán tử redirect từ luồng stream này sang luống stream khác.

```
echo hello 1>/dev/null 2>/dev/null
```

Lệnh này có thể hiểu là sẽ không in ra màn hình tất cả các `output` và `error` bằng các đẩy chúng vào hố `/dev/null`. Chú ý là lệnh `echo` cũng là một loại `standard output`.

`STDIN` : các dữ liệu nhập vào như khi sử dụng lệnh read để nhập dữ liệu từ người dùng

`STDOUT` : Hiển thị kết quả các câu lệnh lên màn hình.


<a name="section94"></a>
### Section 9.4: Using named pipes

Đôi khi bạn có thể muốn xuất một cái gì đó bằng một chương trình và nhập nó vào một chương trình khác, nhưng không thể sử dụng một pipes. (ouput của cái này là input của cái kia).

```
ls -l | grep ".log
```

Bạn có thể chỉ cần ghi vào một tệp tạm thời:

```
touch tempFile.txt
ls -l > tempFile.txt
grep ".log" < tempFile.txt
```

```
[root@vt ~]# touch tempFile.txt
[root@vt ~]# ls -l > tempFile.txt
[root@vt ~]# grep ".log" < tempFile.txt
-rw-r--r--  1 root root    0 Jan  2 16:43 log.txt
```

Điều này hoạt động tốt cho hầu hết các ứng dụng, tuy nhiên, sẽ không ai biết tempFile làm gì và ai đó có thể xóa nó nếu nó chứa đầu ra là `ls -l` trong thư mục đó. Đây là nơi mà một pipe được đặt tên phát huy tác dụng:

```
mkfifo myPipe
ls -l > myPipe
grep ".log" < myPipe
```

```
mkdir pipeFolder
cd pipeFolder
mkfifo myPipe
ls -l
```

<a name="section95"></a>
### Section 9.5: Redirection to network addresses

Bash coi một số paths là đặc biệt và có thể thực hiện network communication bằng cách ghi vào `/dev/{udp|tcp}/host/port`. Bash không thể thiết lập listening server, nhưng có thể khởi tạo kết nối và đối với TCP ít nhất có thể đọc kết quả.

Ví dụ: để gửi một yêu cầu web đơn giản, người ta có thể làm:

```
exec 3</dev/tcp/www.google.com/80
printf 'GET / HTTP/1.0\r\n\r\n' >&3
cat <&3
```

và đã có kết quả của default web page của www.google.com in ra ở stdout.

```
printf 'HI\n' >/dev/udp/192.168.1.1/6666
```

sẽ gửi một message UDP chứa `HI\n` tới một người nghe trên `192.168.1.1:6666`


<a name="section96"></a>
### Section 9.6: Print error messages to stderr

Error messages thường được chứa trong 1 script mục đích để debug và tăng sự thử nghiệm, trải nghiệm.

```
cmd || echo 'cmd failed'
```

có thể hiệu quả với những trường hợp đơn giản nhưng không phải là cách thông thường. Trong ví dụ này, thông báo lỗi sẽ gây nhiễu đầu ra thực tế của tập lệnh bằng cách mix cả error và ouput thành công trong stdout.

Trong 1 cách khác, thông báo lỗi sẽ đến stderr không phải stdout. Nó khá đơn giản:

```
cmd || echo 'cmd failed' >/dev/stderr
```

<a name="section97"></a>
### Section 9.7: Redirecting multiple commands to the same file

```
{
echo "contents of home directory"
ls ~
} > output.txt
```

<a name="section98"></a>
### Section 9.8: Redirecting STDIN

`<` đọc từ đối số bên phải của nó và ghi vào đối số bên trái của nó.

```
[root@vt ~]# echo "b" > /tmp/list.txt
[root@vt ~]# echo "a" >> /tmp/list.txt
[root@vt ~]# echo "c" >> /tmp/list.txt
[root@vt ~]#
[root@vt ~]# sort < /tmp/list.txt
a
b
c
[root@vt ~]#
```

