# Chapter 1 Getting started with Bash

### Mục lục
[Section 1.1: Hello World](#section1)<br>
[Section 1.2: Hello World Using Variables](#section12)<br>
[Section 1.3: Hello World with User Input](#section13)<br>
[Section 1.4: Importance of Quoting in Strings](#section14)<br>
[Section 1.5: Viewing information for Bash built-ins](#section15)<br>
[Section 1.6: Hello World in "Debug" mode](#section16)<br>
[Section 1.7: Handling Named Arguments](#section17)<br>

<a name="section1"></a>
## Section 1.1: Hello World

**Interactive Shell**

Bash shell thường được sử dụng để interactive - tương tác, cho phép nhập, chỉnh sửa và thực thi các dòng lệnh và trả về kết quả. Nhiều hệ điều hành Unix sử dụng bash shell mặc định.

- Lệnh output giã trị

```
[root@bash ~]# echo "DuyDM"
DuyDM
[root@bash ~]#
```

Để thực thi có thể sử dụng `sh`, `bash`...

- Echo

`echo` là một Bash builtin command đưa ra giá trị đầu ra, mặc định sẽ output ra kết quả ở một dòng mới.

**Non-Interactive Shell**

Bash shell cũng có thể chạy dạng non-Interactive - không tương tác giống như một script, không cần sự tương tác của người quản trị. Bất cứ điều gì thực hiện đường bằng dòng lệnh đều có thể đưa vào script.

```
[root@bash ~]# touch hello-world.sh
[root@bash ~]# chmod +x hello-world.sh
[root@bash ~]# vi hello-world.sh
[root@bash ~]# cat hello-world.sh
#!/bin/bash
echo "DuyDM"
[root@bash ~]#
```

Dòng 1 bắt đầu băng ký tự `#!`, `/bin/bash` hướng dẫn chỉ cho đệ điều hành chạy 

VD: `/bin/bash hello-world.sh`

Bash shell chuyển cho nó đường dẫn của tập lệnh như một đối số.


Dòng 2 `echo` là command để đưa giá trị ra thành output.

Để thực thi bash script có thể thực hiện bằng một trong các cách:

```
./hello-world.sh – đây là cách sử dụng phổ biến nhất và được khuyển cáo sử dụng
/bin/bash hello-world.sh
bash hello-world.sh – giả định /bin là $PATH
sh hello-world.sh
```

```
[root@bash ~]# ./hello-world.sh
DuyDM
[root@bash ~]# /bin/bash hello-world.sh
DuyDM
[root@bash ~]# bash hello-world.sh
DuyDM
[root@bash ~]# sh hello-world.sh
DuyDM
[root@bash ~]#
```

Để sử dụng chạy trong production, sẽ bỏ qua phần mở rộng `.sh` (có thể gây hiểu nhầm là lệnh của hệ thống giống lệnh sh giống như các lệnh hệ thống khác như ls, cat).

**Một số lỗi phổ biến:**

- Không áp dụng execute permission cho file dẫn tới lỗi permission kết quả trả về `Permission denied` 

```
chmod +x hello-world.sh
```

```
[root@bash ~]# ./hello-world-1.sh
-bash: ./hello-world-1.sh: Permission denied
[root@bash ~]# chmod +x hello-world-1.sh
[root@bash ~]# ./hello-world-1.sh
DuyDM
[root@bash ~]#
```

- Sửa file script trên Windows, tạo ra các ký tự ở các dòng mà bash không thể xử lý.

Sử dụng `dos2unix` để đưa về định dạng ký tự chuẩn của bash.

```
[root@bash ~]# dos2unix hello-world.sh
dos2unix: converting file hello-world.sh to Unix format ...
[root@bash ~]#
```

- Sử dụng `sh ./hello-world.sh`, không nhận ra rằng `bash` và `sh` là các shell riêng biệt với các đặc điểm riêng biệt mặc dù điều này là vô hại.

Có thể sử dụng một vài đường dẫn khách để gọi chạy bash `#!/usr/bin/env bash` đòi hỏi `/usr/bin/env` phải tồn tại. Trên một số hệ thống `/bin/bash` không tồn tại phải sử dụng `/usr/local/bin/bash` hoặc một số đường dẫn khác.

<a name="section12"></a>
## Section 1.2: Hello World Using Variables

Tạo một file `hello.sh` với một dung và cấp quyền thực thi cho file.

```
#!/usr/bin/env bash

# Note that spaces cannot be used around the `=` assignment operator
whom_variable="DuyDM"

# Use printf to safely output the data
printf "Hello, %s\n" "$whom_variable"
#> Hello, World
```

Không được dùng dấu cách xung quanh toàn tử gán `=` 

Sử dụng `printf` để xuất dữ liệu ra một cách an toàn.

`printf` phải kết hợp `%s\n` để in dữ liệu ra.

```
[root@bash ~]# ./hello.sh
Hello, DuyDM
```

- Sử dụng biến truyền vào khi chạy lệnh `$1` biểu thị  giá trị thứ nhất nhập vào sau đoạn chạy bash, `$2` biểu thị  giá trị thứ nhất nhập vào sau đoạn chạy bash.

```
[root@bash ~]# cat hello1.sh
#!/bin/bash

printf "Hello %s\n" "$1"
[root@bash ~]# ./hello1.sh
Hello
[root@bash ~]# ./hello1.sh DuyDM
Hello DuyDM
[root@bash ~]#
```

It is important to note that $1 has to be quoted in double quote, not single quote. "$1" expands to the first
command line argument, as desired, while '$1' evaluates to literal string $1.

Có một lưu ý quan trọng là "$1" phải được để trong "", "$1" thành đối số giá trị dòng lệnh, '$1' thành chuỗi ký tự.

<a name="section13"></a>
## Section 1.3: Hello World with User Input

- Ý tưởng chạy 1 đoạn bash yêu cầu người dùng nhập vào mội dung `text` trong một biến và biến đó được khai báo trong script.

```
[root@bash ~]# cat hello-input.sh
#!/bin/bash

echo "Who are you?"
read name
echo "Hello, $name."
[root@bash ~]# ./hello-input.sh
Who are you?
DuyDM
Hello, DuyDM.
[root@bash ~]#
```

- Nếu muốn thêm một chuỗi sau biến nhập giá trị đầu vào có thể sử dụng cấu {}.

```
[root@bash ~]# cat hello-input.sh
#!/bin/bash

echo "Who are you?"
read name
echo "Hello, ${name}-DepTrai"
[root@bash ~]# ./hello-input.sh
Who are you?
DuyDM
Hello, DuyDM-DepTrai
[root@bash ~]#
```

<a name="section14"></a>
## Section 1.4: Importance of Quoting in Strings

Quoting - Trích dẫn là quan trọng đối với string trong bash. Kiểm soát việc này có thể kiểm soát được kết quả đầu ra.

- Có 2 kiểu quoting

```
Weak: uses double quotes: "
Strong: uses single quotes: '
```

Nếu muốn hiển thị kết quả với giá trị của đối số truyền về thì sử dụng `Weak Quoting`

```
[root@bash ~]# cat hello3.sh
#!/bin/bash

name="DuyDM"

echo "Hello $name"
[root@bash ~]# ./hello3.sh
Hello DuyDM
```

Nếu không muốn hiển thị kết quả với giá trị của đối số truyền vào thì sử dụng `Strong Quoting`

```
[root@bash ~]# cat hello3.sh
#!/bin/bash

name="DuyDM"

echo 'Hello $name'
[root@bash ~]# ./hello3.sh
Hello $name
```

Sử dụng `\` trước biến để ngăn hiển thị giá trị đối số truyền vào

```
[root@bash ~]# cat hello3.sh
#!/bin/bash

name="DuyDM"

echo "Hello \$name"
[root@bash ~]# ./hello3.sh
Hello $name
```

<a name="section15"></a>
## Section 1.5: Viewing information for Bash built-ins

Các lệnh `built-ins` là các lệnh có sẵn trong linux

```
help <command>
```

Để show lên gợi ý về các tập lệnh, sự phối hợp của các option.


```
[root@bash ~]# help cd
cd: cd [-L|[-P [-e]]] [dir]
    Change the shell working directory.

    Change the current directory to DIR.  The default DIR is the value of the
    HOME shell variable.

    The variable CDPATH defines the search path for the directory containing
    DIR.  Alternative directory names in CDPATH are separated by a colon (:).
    A null directory name is the same as the current directory.  If DIR begins
    with a slash (/), then CDPATH is not used.

    If the directory is not found, and the shell option `cdable_vars' is set,
    the word is assumed to be  a variable name.  If that variable has a value,
    its value is used for DIR.

    Options:
        -L      force symbolic links to be followed
        -P      use the physical directory structure without following symbolic
        links
        -e      if the -P option is supplied, and the current working directory
        cannot be determined successfully, exit with a non-zero status

    The default is to follow symbolic links, as if `-L' were specified.

    Exit Status:
    Returns 0 if the directory is changed, and if $PWD is set successfully when
    -P is used; non-zero otherwise.
```

- Xem tất cả các tập lệnh có sẵn trong hệ thống Linux

```
help -d
```

<a name="section16"></a>
## Section 1.6: Hello World in "Debug" mode

Sử dụng tham số `-x` để xem debug từ dòng lệnh.

```
[root@bash ~]# cat hello-world.sh
#!/bin/bash
echo "DuyDM"
[root@bash ~]# bash -x hello-world.sh
+ echo DuyDM
DuyDM
[root@bash ~]#
```

Ví dụ:

```
[root@bash ~]# cat test.sh
#!/bin/bash

echo "Hello World\n"

adding_string_to_number="s"
v=$(expr 5 + $adding_string_to_number)
[root@bash ~]#
[root@bash ~]#
[root@bash ~]#
[root@bash ~]# bash test.sh
Hello World\n
expr: non-integer argument
[root@bash ~]#
[root@bash ~]#

[root@bash ~]#
[root@bash ~]# bash -x test.sh
+ echo 'Hello World\n'
Hello World\n
+ adding_string_to_number=s
++ expr 5 + s
expr: non-integer argument
+ v=
[root@bash ~]#
```

Khi sử dụng debug `-x` sẽ thấy được toán tử của phép + không hợp lệ.


<a name="section17"></a>
## Section 1.7: Handling Named Arguments


