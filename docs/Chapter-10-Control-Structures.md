# Chapter 10 Control Structures

```
File Operators Details (Toán tử liên quan đến file)
-e "$file"     Returns true if the file exists : Trả về true nếu tệp tồn tại
-d "$file"     Returns true if the file exists and is a directory: Trả về true nếu tồn tại và là một thư mục
-f "$file"     Returns true if the file exists and is a regular file: Trả về true nếu tồn tại và là tệp thông thường
-h "$file"     Returns true if the file exists and is a symbolic link: Trả về true nếu tồn tại và là một liên kết tượng trưng liên kết(a symbolic link)
```

```
String Comparators Details (so sáng chuỗi)
-z "$str"          True if length of string is zero: True nếu độ dài chuỗi =0
-n "$str           True if length of string is non-zero: 	True nếu độ dài chuỗi khác 0
"$str" = "$str2"   True if string $str is equal to string $str2. Not best for integers. It may work but will be
inconsitent: 	True nếu chuỗi $str bằng chuỗi $str2
"$str" != "$str2"  True if the strings are not equal: 	True nếu chuỗi $str không bằng chuỗi $str2
```

```
Integer Comparators    Details: So sánh số nguyên
"$int1" -eq "$int2"    True if the integers are equal: equal – đúng nếu các số nguyên bằng nhau
"$int1" -ne "$int2"    True if the integers are not equals: not equals – đúng nếu các số nguyên không bằng nhau
"$int1" -gt "$int2"    True if int1 is greater than int2: greater than – đúng nếu $int1 lớn hơn $int2
"$int1" -ge "$int2"    True if int1 is greater than or equal to int2: greater than or equal – đúng nếu $int1 lớn hơn hoặc bằng $int2
"$int1" -lt "$int2"    True if int1 is less than int2: less than – đúng nếu $int1 Nhỏ hơn $int2
"$int1" -le "$int2"    True if int1 is less than or equal to int2: less than or equal – đúng nếu $int1 Nhỏ hơn hoặc bằng $int2
```


### Mục lục
[Section 10.1: Conditional execution of command lists](#section101)<br>
[Section 10.2: If statement](#section102)<br>
[Section 10.3: Looping over an array](#section103)<br>
[Section 10.4: Using For Loop to List Iterate Over Numbers](#section104)<br>
[Section 10.5: continue and break](#section105)<br>
[Section 10.6: Loop break](#section106)<br>
[Section 10.7: While Loop](#section107)<br>
[Section 10.8: For Loop with C-style syntax](#section108)<br>
[Section 10.9: Until Loop](#section109)<br>
[Section 10.10: Switch statement with case](#section1010)<br>
[Section 10.11: For Loop without a list-of-words parameter](#section1011)<br>

<a name="section101"></a>
### Section 10.1: Conditional execution of command lists

Sử dụng lệnh thực thi có điều kiện.

Bất kỳ command, expression - biểu thức hoặc function trong nào cũng như bất kỳ lệnh hoặc tập lệnh bên ngoài nào đều có thể được thực thi có điều kiện bằng cách sử dụng các toán tử `&& (và)` và `|| (hoặc)`.

Ví dụ: Di chuyển tới một thư mục và hiện thị đường dẫn của thư mục đó.

```
cd my_directory && pwd
```

```
[root@vt ~]# cd my_directory && pwd
/root/my_directory
[root@vt my_directory]#
```

- Thoát nếu lệnh cd trả về giá trị fail

```
cd my_directory || exit
rm -rf *
```

Khi kết hợp nhiều câu lệnh theo cách này, điều quan trọng cần nhớ là (không giống như nhiều ngôn ngữ kiểu C) các toán tử này không có quyền ưu tiên và là phép kết hợp trái.

```
cd my_directory && pwd || echo "No such directory d"
```

```
[root@vt my_directory]# cd my_directory && pwd || echo "No such directory d"
-bash: cd: my_directory: No such file or directory
No such directory d
[root@vt my_directory]# cd
[root@vt ~]# cd my_directory && pwd || echo "No such directory d"
/root/my_directory
[root@vt my_directory]#
```

Nếu lệnh `cd` thực hiện thành công và lệnh `pwd` được thực hiện sẽ in kết quả đường dẫn hiện tại. Ngược lại nếu cd fail sẽ bỏ qua lệnh pwd thực hiện lệnh echo "No such directory d".

```
cd my_directory && ls || echo "No such directory
```

Nếu lệnh cd đúng thì ls sẽ được thực thi và bỏ qua echo.
     Nếu ls đúng thì || echo sẽ được bỏ qua.
     Nếu ls fail thì || echo sẽ được thực thi

Nếu lệnh cd sai thì bỏ qua lệnh ls và thực thi lệnh echo.

**Why use conditional execution of command lists**

Thực thi có điều kiện nhanh hơn `if ... then` nhưng lợi thế chính của nó là cho phép các chức năng và tập lệnh thoát sớm, hoặc "short circui".
Không giống như nhiều ngôn ngữ như C, nơi bộ nhớ được cấp phát rõ ràng cho các cấu trúc và biến và như vậy (và do đó phải được phân bổ), bash xử lý điều này dưới các lớp phủ. Trong hầu hết các trường hợp, chúng tôi không phải dọn dẹp bất cứ thứ gì trước khi rời khỏi function. Một câu lệnh trả về sẽ deallocate mọi thứ cục bộ cho function và thực thi nhận tại địa chỉ trả về  trên ngăn xếp.
Do đó, return từ function hoặc thoát script càng sớm càng tốt có thể cải thiện đáng kể hiệu suất và giảm tải hệ thống bằng cách tránh thực thi mã không cần thiết. Ví dụ... 

```
my_function () {
### ALWAYS CHECK THE RETURN CODE
# one argument required. "" evaluates to false(1)
[[ "$1" ]] || return 1
# work with the argument. exit on failure
do_something_with "$1" || return 1
do_something_else || return 1
# Success! no failures detected, or we wouldn't be here
return 0
}
```

<a name="section102"></a>
## Section 10.2: If statement

```
if [[ $1 -eq 1 ]]; then
echo "1 was passed in the first parameter"
elif [[ $1 -gt 2 ]]; then
echo "2 was not passed in the first parameter"
else
echo "The first parameter was not 1 and is not more than 2."
fi
```

Khóa lệnh `fi` là cần thiết nhưng có thể có elif and/or the else hoặc các mệnh đề khác.

Dấu chấm phẩy trước đó là cú pháp tiêu chuẩn để kết hợp hai lệnh trên một dòng `;` chúng chỉ có thể được bỏ qua nếu sau đó được chuyển sang dòng tiếp theo.

Điều quan trọng là phải hiểu rằng dấu ngoặc `[[`không phải là một phần của cú pháp, nhưng được coi như một lệnh`;` nó là mã thoát khỏi lệnh này đang được kiểm tra. Do đó, bạn phải luôn bao gồm các khoảng trắng xung quanh dấu ngoặc.

- Điều này cũng có nghĩa là kết quả của bất kỳ lệnh nào cũng có thể được kiểm tra. Nếu mã thoát khỏi lệnh là số 0, câu lệnh được coi là đúng.

```
if grep "foo" bar.txt; then
echo "foo was found"
else
echo "foo was not found"
fi
```

- Các biểu thức toán học, khi được đặt bên trong dấu ngoặc kép`((   ))`, cũng trả về 0 hoặc 1 theo cách tương tự và cũng có thể được kiểm tra:


```
if (( $1 + 5 > 91 )); then
echo "$1 is greater than 86"
fi
```

- Bạn cũng có thể bắt gặp câu lệnh `if` có dấu ngoặc đơn. Chúng được xác định trong tiêu chuẩn POSIX và được đảm bảo hoạt động trong tất cả các shells tương thích với POSIX bao gồm cả Bash. Cú pháp rất giống với cú pháp trong Bash:


```
if [ "$1" -eq 1 ]; then
echo "1 was passed in the first parameter"
elif [ "$1" -gt 2 ]; then
echo "2 was not passed in the first parameter"
else
echo "The first parameter was not 1 and is not more than 2."
fi
```

<a name="section103"></a>
### Section 10.3: Looping over an array

- `for loop:`

```
arr=(a b c d e f)
for i in "${arr[@]}";do
echo "$i"
done
```

hoặc

```
arr=(a b c d e f)
for ((i=0;i<${#arr[@]};i++));do
echo "${arr[$i]}"
done
```

- while loop:

```
arr=(a b c d e f)
i=0
while [ $i -lt ${#arr[@]} ];do
echo "${arr[$i]}"
i=$(expr $i + 1)
done
```

Hoặc


```
arr=(a b c d e f)
i=0
while (( $i < ${#arr[@]} ));do
echo "${arr[$i]}"
((i++))
done
```

<a name="section104"></a>
### Section 10.4: Using For Loop to List Iterate Over Numbers

```
for i in {1..10}; do # {1..10} expands to "1 2 3 4 5 6 7 8 9 10"
echo $i
done
```

Output

```
1
2
3
4
5
6
7
8
9
10
```

<a name="section105"></a>
### Section 10.5: continue and break

- Example for continue


```
for i in [series]
do
   command 1
   command 2
   if (condition) # Condition to jump over command 3
           continue # skip to the next value in "series"
   fi
   command 3
done
```

- Example for break

```
for i in [series]
do
    command 4
    if (condition) # Condition to break the loop
    then
        command 5 # Command if the loop needs to be broken
        break
    fi
    command 6 # Command to run if the "condition" is never true
done
```

<a name="section106"></a>
### Section 10.6: Loop break

- Break multiple loop:

```
arr=(a b c d e f)
for i in "${arr[@]}";do
    echo "$i"GoalKicker.com – Bash Notes for Professionals 38
    for j in "${arr[@]}";do
        echo "$j"
        break 2
    done
done
```

Output:

```
a
a
```

- Break single loop:

```
arr=(a b c d e f)
for i in "${arr[@]}";do
    echo "$i"
    for j in "${arr[@]}";do
        echo "$j"
        break
    done
done
```

Output:

```
a
a
b
a
c
a
d
a
e
a
f
a
```

<a name="section107"></a>
### Section 10.7: While Loop

```
#! /bin/bash

i=0

while [ $i -lt 5 ] #While i is less than 5
do
    echo "i is currently $i"
    i=$[$i+1] #Not the lack of spaces around the brackets. This makes it a not a test expression
done #ends the loop
```

Chú ý rằng có khoảng trống xung quanh dấu ngoặc trong quá trình kiểm tra (sau câu lệnh while). Những không gian này là cần thiết.

```
This loop outputs:
i is currently 0
i is currently 1
i is currently 2
i is currently 3
i is currently 4
```

<a name="section108"></a>
### Section 10.8: For Loop with C-style syntax

Định dạng cơ bản của C-style for loop là:

```
for (( variable assignment; condition; iteration process ))
```

Việc gán biến bên trong vòng lặp C-style for có thể chứa các khoảng trắng không giống như phép gán thông thường.

Các biến bên trong vòng lặp for kiểu C không được đặt trước $.

```
for (( i = 0; i < 10; i++ ))
do
    echo "The iteration number is $i"
done
```

Ngoài ra, chúng ta có thể xử lý nhiều biến bên trong C-style for loop:

```
for (( i = 0, j = 0; i < 10; i++, j = i * i ))
do
    echo "The square of $i is equal to $j"
done
```

<a name="section109"></a>
### Section 10.9: Until Loop

Vòng lặp Until thực thi cho đến khi điều kiện là true

```
i=5
until [[ i -eq 10 ]]; do #Checks if i=10
echo "i=$i" #Print the value of i
    i=$((i+1)) #Increment i by 1
done
```

Output:

```
i=5
i=6
i=7
i=8
i=9
```

Khi đạt đến 10, điều kiện trong vòng lặp cho đến khi trở thành true và vòng lặp kết thúc.

<a name="section1010"></a>
### Section 10.10: Switch statement with case

Với câu lệnh case, bạn có thể so khớp các giá trị với một biến.

Đối số được truyền cho trường hợp được mở rộng và cố gắng khớp với từng mẫu.

Nếu một kết quả phù hợp được tìm thấy, các lệnh tối đa `;;` được thực hiện.

```
case "$BASH_VERSION" in
  [34]*)
      echo {1..4}
      ;;
  *)
      seq -s" " 1 4
esac
```

Pattern không phải là biểu thức chính quy mà là pattern matching.

<a name="section1011"></a>
### Section 10.11: For Loop without a list-of-words parameter

```
for arg; do
    echo arg=$arg
done
```

Thay vào đó, một vòng lặp `for` không có tham số danh sách các từ sẽ lặp qua các tham số vị trí. Nói cách khác, ví dụ trên hình dung qua code này:

```
for arg in "$@"; do
    echo arg=$arg
done
```

In other words, if you catch yourself writing for i in "$@"; do ...; done, just drop the in part, and write simply for i; do ...; done.

Nói cách khác, nếu bạn bắt gặp mình đang viết `for i in "$@"; do ...; done,`, chỉ cần bỏ một phần, và viết đơn giản ` for i; do ...; done.`




