# Chapter 5: Using cat

```
Option Details
-n Print line numbers
-v Show non-printing characters using ^ and M- notation except LFD and TAB
-T Show TAB characters as ^I
-E Show linefeed(LF) characters as $
-e Same as -vE
-b Number nonempty output lines, overrides -n
-A equivalent to -vET
-s suppress repeated empty output lines, s refers to squeeze
```

### Mục lục
[Section 5.1: Concatenate files](#section51)<br>
[Section 5.2: Printing the Contents of a File](#section52)<br>
[Section 5.3: Write to a file](#section53)<br>
[Section 5.4: Show non printable characters](#section54)<br>
[Section 5.5: Read from standard input](#section55)<br>
[Section 5.6: Display line numbers with output](#section56)<br>
[Section 5.7: Concatenate gzipped files](#section57)<br>

<a name="section51"></a>
### Section 5.1: Concatenate files

```
[root@vt ~]# rm -rf name.txt
[root@vt ~]# cat f1.txt
do
[root@vt ~]# cat f2.txt
manh
[root@vt ~]# cat f3.txt
duy
[root@vt ~]# cat f1.txt f2.txt f3.txt > name.txt
[root@vt ~]# cat name.txt
do
manh
duy
[root@vt ~]#
```

```
[root@vt ~]# cat f1.txt f2.txt f3.txt | grep do
do
[root@vt ~]#
```

<a name="section52"></a>
### Section 5.2: Printing the Contents of a File

```
cat file.txt
```

Sẽ hiển thị nội dung của một file.

Nếu tệp chứa các ký tự không phải ASCII, bạn có thể hiển thị các ký tự đó một cách tượng trưng bằng `cat -v`. 

```
cat -v unicode.txt
```

Để sử dụng tương tác với dile như `less` hoặc `more` hơn. (less thì mạnh hơn more và nên sử dụng less thường xuyên hơn more).

```
less file.txt
```

`tr` cũng dùng để hiển thị nội dung file.

```
[root@vt ~]# tr A-Z a-z < name.txt
do
manh
duy
[root@vt ~]#
```

`tac` hiển thị nôi dung ngược của file.

```
[root@vt ~]# tac name.txt
duy
manh
do
[root@vt ~]#
```

Cat theo số dùng.

```
cat -n file.txt
```

<a name="section53"></a>
### Section 5.3: Write to a file

```
cat >file
```

```
cat <<END > duydm
Domanhduy
END
```

```
[root@vt ~]# cat <<END > duydm
> Domanhduy
> END
[root@vt ~]# cat duydm
Domanhduy
[root@vt ~]#
```

Cho phép viết văn bản trên terminal sẽ được lưu trong một tệp có tên tệp là `file`

```
cat >>file
```

```
cat <<END >> duydm
system
END
```

```
[root@vt ~]# cat duydm
Domanhduy
[root@vt ~]#
[root@vt ~]# cat <<END >> duydm
> system
> END
[root@vt ~]# cat duydm
Domanhduy
system
[root@vt ~]#
```

sẽ làm tương tự, ngoại ra sẽ nối văn bản vào cuối tệp.

`Ctrl + D` để kết thúc việc viết văn bản.

Hướng dẫn dưới đây có thể sử dụng để viết nội dung của một file vào dòng lệnh hoặc script.

```
cat <<END >file
Hello, World.
END
```

Một chuỗi token sau `<<`  là ký tự chuyển hướng là một chuỗi tùy ý phải trình diễn trên một dòng (không có khoảng trắng ở đầu hoặc cuối) để cho biết đây là phần cuối của tài liệu.


```
cat <<'fnord'
Nothing in `here` will be $changed
fnord
```

(Không có dấu ngoặc kép, ở đây sẽ được thực thi dưới dạng lệnh và `$change` sẽ được thay thế bằng giá trị của biến đã thay đổi -- hoặc không có gì, nếu nó không được xác định.)

<a name="section54"></a>
### Section 5.4: Show non printable characters

Để xem các ký tự không thuộc bảng mã ASCII

Nếu bạn đã sao chép mã từ web, bạn có thể có các dấu ngoặc kép như "thay vì ký tự chuẩn".

```
$ cat -v file.txt
$ cat -vE file.txt # Useful in detecting trailing spaces.
```


```
[root@vt ~]# echo '” ' | cat -vE
M-bM-^@M-^] $
[root@vt ~]#
```

Bạn cũng có thể muốn sử dụng cat -A (A for All) tương đương với cat -vET. Nó sẽ hiển thị các ký tự TAB (được hiển thị dưới dạng ^ I), các ký tự không in được và ở cuối mỗi dòng:

```
[root@vt ~]# echo '” `' | cat -A
M-bM-^@M-^] `$
[root@vt ~]#
```

<a name="section55"></a>
### Section 5.5: Read from standard input

```
cat < file.txt
```

Output giống như cat file.txt, nhưng nó đọc nội dung của tệp từ đầu vào chuẩn thay vì trực tiếp từ tệp.

```
printf "first line\nSecond line\n" | cat -n
```

```
[root@vt ~]# ./bash.sh
     1  first line
     2  Second line
[root@vt ~]#
```

Lệnh `echo` trước `|` xuất ra hai dòng. Lệnh `cat` hoạt động trên đầu ra để thêm số dòng.

<a name="section56"></a>
### Section 5.6: Display line numbers with output

Sử dụng cờ `--number` để in số dòng trước mỗi dòng. Ngoài ra, `-n` cũng làm điều tương tự.

```
[root@vt ~]# cat --number duydm
     1  Domanhduy
     2  system
[root@vt ~]# cat -n duydm
     1  Domanhduy
     2  system
[root@vt ~]#
```

Để bỏ qua các dòng trống khi đếm dòng, hãy sử dụng `--number-nonblank`, hoặc đơn giản là `-b`

```
[root@vt ~]# cat duydm
Domanhduy
system

network
[root@vt ~]# cat -n duydm
     1  Domanhduy
     2  system
     3
     4  network
[root@vt ~]# cat -b duydm
     1  Domanhduy
     2  system

     3  network
[root@vt ~]#
```

<a name="section57"></a>
### Section 5.7: Concatenate gzipped files

Các tệp được nén bởi `gzip` có thể được nối trực tiếp thành các tệp `gzip` lớn hơn.

```
cat file1.gz file2.gz file3.gz > combined.gz
```

Đây là một thuộc tính của gzip kém hiệu quả hơn việc nối các tệp đầu vào và giải nén:

```
cat file1 file2 file3 | gzip > combined.gz
```

Ví dụ:

```
[root@vt ~]# echo 'Hello world!' > hello.txt
[root@vt ~]# echo 'Howdy world!' > howdy.txt
[root@vt ~]# gzip hello.txt
[root@vt ~]# gzip howdy.txt
```

```
[root@vt ~]# cat hello.txt.gz howdy.txt.gz > greetings.txt.gz
[root@vt ~]# gunzip greetings.txt.gz
[root@vt ~]# cat greetings.txt
Hello world!
Howdy world!
```

Lưu ý rằng `greetings.txt.gz` là một tệp duy nhất và được giải nén dưới dạng tệp đơn `greetings.txt`. Ngược lại điều này với `tar -czf hello.txt howdy.txt> greetings.tar.gz`, giữ các tệp riêng biệt bên trong.

